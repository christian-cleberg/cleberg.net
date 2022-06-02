+++
date = 2021-04-23
title = "Roll Your Own Static Commenting System in PHP"
description = "Learn how to skip the tedium of finding the proper commenting system by simply rolling your own in PHP."
draft = false
aliases = ["/blog/roll-your-own-static-commenting-system-in-php"]
+++

## The Terrible-ness of Commenting Systems

The current state of affairs regarding interactive comment systems is, well,
terrible. It is especially awful if you're a privacy conscious person who does
not generally load third-party scripts or frames on the websites you visit.

Even further, many comment systems are charging exorbitant fees for something
that should be standard.

Of course, there are some really terrible options:

-   Facebook Comments
-   Discourse

There are some options that are better but still use too many scripts, frames,
or social integrations on your web page that could impact some users:

-   Disqus
-   Isso
-   Remark42

Lastly, I looked into a few unique ways of generating blog comments, such as
using Twitter threads or GitHub issues to automatically post issues. However,
these both rely on external third-party sites that I don't currently use.

## Stay Static with Server-Side Comments

The main issue for my personal use-case is that my blog is completely, 100%
static. I use PHP on the back-end but website visitors only see HTML and a
single CSS file. No external javascript and no embedded frames.

So, how do we keep a site static and still allow users to interact with blog
posts? The key actually pretty simple - I'm already using PHP, so why not rely
on the classic HTML `<form>` and a PHP script to save the comments somewhere? As
it turns out, this was a perfect solution for me.

The second issue for my personal use-case is that I am trying to keep the
contents of my website accessible over time, as described by @brandur, in his
post entitled
[Blog with Markdown + Git, and degrade gracefully through time](https://brandur.org/fragments/graceful-degradation-time)
.

This means I cannot rely on a database for comments, since I do not rely on a
database for any other part of my websites.

I blog in plain Markdown files, commit all articles to Git, and ensure that
future readers will be able to see the source data long after I'm gone, or the
website has gone offline. However, I still haven't committed any images served
on my blog to Git, as I'm not entirely sold on Git LFS yet - for now, images can
be found at [img.cleberg.io](https://img.cleberg.io).

Saving my comments back to the Git repository ensures that another aspect of my
site will degrade gracefully.

## Create a Comment Form

Okay, let's get started. The first step is to create an HTML form that users can
see and utilize to submit comments. This is fairly easy and can be changed
depending on your personal preferences.

Take a look at the code block below for the form I currently use. Note that
`<current-url>` is replaced automatically in PHP with the current post's URL, so
that my PHP script used later will know which blog post the comment is related
to.

The form contains the following structure:

1. `<form>` - This is the form and will determine which PHP script to send the
   comment to.
2. `<section hidden>` - This section is hidden from the user and is used to
   ensure that we know which blog post sent the comment.
3. `<section>` Display Name (Optional) - Used to accept a display name, if
   entered.
4. `<section>` Comment (Required) - Used to accept the user's full comment.
   Markdown is allowed.
5. `<button>` - A button to submit the form.

```html
<form action="/comment.php" method="POST">
	<h3>Leave a Comment</h3>
	<section hidden>
		<label class="form-label" for="postURL">Post URL</label>
		<input
			class="form-control"
			id="postURL"
			name="postURL"
			type="text"
			value="<current-url>"
		/>
	</section>
	<section>
		<label class="form-label" for="userName">Display Name</label>
		<input
			class="form-control"
			id="userName"
			name="userName"
			placeholder="John Doe"
			type="text"
		/>
	</section>
	<section>
		<label class="form-label" for="userContent">Your Comment</label>
		<textarea
			class="form-control"
			id="userContent"
			name="userContent"
			rows="3"
			placeholder="# Feel free to use Markdown"
			aria-describedby="commentHelp"
			required
		></textarea>
		<div id="commentHelp" class="form-text">
			Comments are saved as Markdown and cannot be edited or deleted.
		</div>
	</section>
	<button type="submit">Submit</button>
</form>
```

## Handle Comments via POST

Now that we have a form and can submit comments, we need to create a PHP script
so that the server can fetch the comment data and save it. Make sure your PHP
script name matches the name you entered in the `action` field in your form.

See the code block below for the contents of my `comment.php` script. We perform
the following tasks in this script:

1. Grab the POST data from the HTML form.
2. Sanitize the comment data with `htmlentities`.
3. Set the display name to `Anonymous` if it was left blank.
4. Create a PHP object that combines all of this data.
5. Check if our destination file `comments.json` exists.
6. If so, convert the PHP object to JSON and save it to the file.
7. If the `comments.json` file does not exist, the script will exit with an
   error. You can alter this to ensure it creates the script, but my source code
   includes the file by default, so it will always exist.
8. Finally, send the user back to the `#comments` section of the blog post they
   just read.

```php
// Get the content sent from the comment form
$comment = htmlentities($_POST['userContent']);
$post_url = $_POST['postURL'];

// Set default values if blank
if (isset($_POST['userName']) && trim($_POST['userName']) !== "") {
    $username = $_POST['userName'];
} else {
    $username = 'Anonymous';
}

// Create an empty PHP object
$user_object = new stdClass();

// Add object content
$user_object->timestamp = date('Y-m-d H:i:s');
$user_object->username = $username;
$user_object->comment = $comment;
$user_object->post_url = $post_url;

// Append JSON to file
$file_name = 'comments.json';
if (file_exists($file_name)) {
    $source_data = file_get_contents($file_name);
    $temp_array = json_decode($source_data);
    array_push($temp_array, $user_object);
    $json_data = json_encode($temp_array, JSON_PRETTY_PRINT);
    file_put_contents($file_name, $json_data);
} else {
    die('Error: The "comments.json" file does not exist.');
}

// Send the user back
header('Location: ' . $post_url . '#comments');
```

If you're using Apache, make sure the `www-data` user on your server has the
correct permissions to your website directory or else it will not be able to
write to `comments.json`.

```bash
chgrp -R www-data /path/to/website/
chmod -R g+w comments.json
```

## Display User Comments

Now that we can submit comments, and they are saved to the `comments.json` file,
let's make sure we can show those comments in each blog post.

The code block below shows the function I use to decode my `comments.json` file,
check if the comments apply to the current post, and then display them.

This piece of code should **really** be inside a function (or at least in an
organized PHP workflow). Don't just copy-and-paste and expect it to work. You
need to at least supply a `$query` variable depending on the page visited.

```php
$query = 'your-blog-post.html';

// Load saved comments
$comments_file = 'comments.json';
$comments_raw = file_get_contents($comments_file);
$comments = json_decode($comments_raw);
$comment_section = '<section id="comments" class="comments"><h3>Comments</h3>';
foreach ($comments as $comment) {
    if ($comment->post_url == "https://example.com/post/" . $query) {
        // Assign metadata to variables
        $comment_timestamp = $comment->timestamp;
        $comment_username = $comment->username;
        $comment_content = $comment->comment;

        // Parse the comment, in case it contains Markdown
        $comment_md = Parsedown::instance()->text($comment_content);
        $comment_html = new DOMDocument();
        $comment_html->loadHTML($comment_md);
        $comment_html_links = $comment_html->getElementsByTagName('a');
        foreach ($comment_html_links as $comment_html_link) {
            $comment_html_link->setAttribute('rel', 'noopener,noreferrer');
            $comment_html_link->setAttribute('target', '_blank');
        }
        $comment_secured_html = $comment_html->saveHTML();

        // Apply metadata to comments section
        $comment_section .= '<div class="user-comment"><div class="row"><label>Timestamp:</label><p>' . $comment_timestamp . '</p></div><div class="row"><label>Name:</label><p>' . $comment_username . '</p></div><div class="row markdown"><label>Comment:</label><div class="comment-markdown">' . $comment_secured_html . '</div></div></div>';
    }
}

echo $comment_section;
```

## Bonus: Create a 'Recent Comments' Page

Finally, the last part of my current system is to create a Recent Comments page
so that I can easily check-in on my blog and moderate any spam. As an
alternative, you could use PHP's `mail()` function to email you for each blog
comment.

The code to do this is literally the same code as the previous section, I just
make sure it is printed when someone visits `https://example.com/comments/`.

## Possible Enhancements

This comment system is by no means a fully-developed system. I have noted a few
possible enhancements here that I may implement in the future:

-   Create a secure moderator page with user authentication at
    `https://blog.example.com/mod/`. This page could have the option to edit or
    delete any comment found in `comments.json`.
-   Create a temporary file, such as `pending_comments.json`, that will store
    newly-submitted comments and won't display on blog posts until approved by a
    moderator.
-   Create a `/modlog/` page with a chronological log, showing which moderator
    approved which comments and why certain comments were rejected.
