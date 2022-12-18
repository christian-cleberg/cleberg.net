+++
date = 2021-07-15
title = "How to Delete All GitLab Repositories"
description = "Learn how to delete all GitLab repositories in your user account."
draft = false
aliases = ["/blog/how-to-delete-all-gitlab-repositories"]
+++

## Background

Have you ever used GitLab to host your source code, moved to a different host,
and wanted to delete everything from your GitLab account? Well this post covers
any scenario where you would want to delete all repositories from your GitLab
account.

For me, I currently maintain around 30 repositories and don't like to manually
delete them whenever I switch hosts. GitHub has a few different tools online to
delete all repositories for you, but I have not found anything similar for
GitLab, so I needed an alternative solution.

## Use a Python Script

### Requirements

Before we look at the script, make sure you know your GitLab username. Next,
[create an authorization token](https://gitlab.com/-/profile/personal_access_tokens)
so that the Python script can delete your repositories. Don't lose this token or
else you'll need to create a new one.

### Create the Script

To run a Python script, you must first create it. Open a terminal and enter the
following commands in whichever directory you prefer to store the script. You
can do the same things in a file manager, if you prefer.

```sh
mkdir delete-gitlab
```

```sh
cd delete-gitlab
```

```sh
nano main.py
```

Enter the following code into your `main.py` script.

```python
import request
import json


def get_project_ids():
    url = "https://gitlab.com/api/v4/users/{user-id}/projects"

    querystring = {"owned": "true", "simple": "true", "per_page": "50"}

    payload = ""
    headers = {'authorization': 'Bearer {auth-token}'}

    response = requests.request("GET", url, data=payload, headers=headers, params=querystring)

    projects = json.loads(response.text)
    projects_ids = list(map(lambda project: project.get('id'), projects))

    return projects_ids


def remove_project(project_id):
    url_temp = "https://gitlab.com/api/v4/projects/{project}"
    headers = {'authorization': 'Bearer {auth-token}'}
    querystring = ""
    payload = ""

    url = url_temp.format(project=project_id)

    response = requests.request("DELETE", url, data=payload, headers=headers, params=querystring)
    project = json.loads(response.text)
    print(project)


def main():
    projects_ids = get_project_ids()

    url_temp = "https://gitlab.com/api/v4/projects/{project}"
    headers = {'authorization': 'Bearer {auth-token}'}
    querystring = ""
    payload = ""

    for project_id in projects_ids:
        url = url_temp.format(project=project_id)

        response = requests.request("GET", url, data=payload, headers=headers, params=querystring)
        project = json.loads(response.text)
        print(str(project.get('id')) + " " + project.get('name'))
        print("Removing...")
        remove_project(project_id)


if __name__ == "__main__":
    main()
```

Now that you have the proper information, replace `{user-id}` with your GitLab
username and `{auth-token}` with the authorization token you created earlier.

Finally, simply run the script and watch the output. You can also use PyCharm
Community Edition to edit and run the Python script, if you don't want to work
in a terminal.

```sh
python3 main.py
```
