+++
date = 2020-09-01
title = "IBM Watson Visual Recognition"
description = "Learn how to use IBM Watson visual recognition to automatically analyze images."
draft = false
aliases = ["/blog/ibm-watson-visual-recognition"]
+++

## What is IBM Watson?

If you've never heard of [Watson](https://www.ibm.com/watson), this service is a
suite of enterprise-ready AI services, applications, and tooling provided by
IBM. Watson contains quite a few useful tools for data scientists and students,
including the subject of this post today: visual recognition.

If you'd like to view the official documentation for the Visual Recognition API,
visit the
[API Docs](https://cloud.ibm.com/apidocs/visual-recognition/visual-recognition-v3?code=python).

## Prerequisites

To be able to use Watson Visual Recognition, you'll need the following:

1. Create a free account on
   [IBM Watson Studio](https://www.ibm.com/cloud/watson-studio).
2. Add the
   [Watson Visual Recognition](https://www.ibm.com/cloud/watson-visual-recognition)
   service to your IBM Watson account.
3. Get your API key and URL. To do this, first go to the
   [profile dashboard](https://dataplatform.cloud.ibm.com/home2?context=cpdaas)
   for your IBM account and click on the Watson Visual Recognition service you
   created. This will be listed in the section titled **Your services**. Then
   click the **Credentials** tab and open the **Auto-generated credentials**
   dropdown. Copy your API key and URL so that you can use them in the Python
   script later.
4. **[Optional]** While not required, you can also create the Jupyter Notebook
   for this project right inside
   [Watson Studio](https://www.ibm.com/cloud/watson-studio). Watson Studio will
   save your notebooks inside an organized project and allow you to use their
   other integrated products, such as storage containers, AI models,
   documentation, external sharing, etc.

## Calling the IBM Watson Visual Recognition API

Okay, now let's get started.

To begin, we need to install the proper Python package for IBM Watson.

```bash
pip install --upgrade --user "ibm-watson>=4.5.0"
```

Next, we need to specify the API key, version, and URL given to us when we
created the Watson Visual Recognition service.

```python
apikey = "<your-apikey>"
version = "2018-03-19"
url = "<your-url>"
```

Now, let's import the necessary libraries and authenticate our service.

```python
import json
from ibm_watson import VisualRecognitionV3
from ibm_cloud_sdk_core.authenticators import IAMAuthenticator

authenticator = IAMAuthenticator(apikey)
visual_recognition = VisualRecognitionV3(
  version=version,
  authenticator=authenticator
)

visual_recognition.set_service_url(url)
```

**[Optional]** If you'd like to tell the API not to use any data to improve
their products, set the following header.

```python
visual_recognition.set_default_headers({'x-watson-learning-opt-out': "true"})
```

Now we have our API all set and ready to go. For this example, I'm going to
include a `dict` of photos to load as we test out the API.

```python
data = [
  {
    "title": "Grizzly Bear",
    "url": "https://example.com/photos/20140717.jpg"
  },
  {
    "title": "Nature Lake",
    "url": "https://example.com/photos/20140718.jpg"
  },
  {
    "title": "Welcome Sign",
    "url": "https://example.com/photos/20190608_02.jpg"
  },
  {
    "title": "Honey Badger",
    "url": "https://example.com/photos/20190611_03.jpg"
  },
  {
    "title": "Grand Canyon Lizard",
    "url": "https://example.com/photos/20190612.jpg"
  },
  {
    "title": "Castle",
    "url": "https://example.com/photos/20191116_01.jpg"
  }
]
```

Now that we've set up our libraries and have the photos ready, let's create a
loop to call the API for each image. The code below shows a loop that calls the
URL of each image and sends it to the API, requesting results with at least 60%
confidence. The results are output to the console with dotted lines separating
each section.

In the case of an API error, the codes and explanations are output to the
console.

```python
from ibm_watson import ApiException

for x in range(len(data)):
try:
   url = data[x]["url"]
   images_filename = data[x]["title"]
   classes = visual_recognition.classify(
       url=url,
       images_filename=images_filename,
       threshold='0.6',
       owners=["IBM"]).get_result()
   print("-----------------------------------------------")
   print("Image Title: ", data[x]["title"], "\n")
   print("Image URL: ", data[x]["url"], "\n")
   classification_results = classes["images"][0]["classifiers"][0]["classes"]
   for result in classification_results:
       print(result["class"], "(", result["score"], ")")
   print("-----------------------------------------------")
except ApiException as ex:
   print("Method failed with status code " + str(ex.code) + ": " + ex.message)
```

## The Results

Here we can see the full result set of our function above. If you view each of
the URLs that we sent to the API, you'll be able to see that it was remarkably
accurate. To be fair, these are clear high-resolution, clear photos shot with a
professional camera. In reality, you will most likely be processing images that
are lower quality and may have a lot of noise in the photo.

However, we can clearly see the benefit of being able to call this API instead
of attempting to write our own image recognition function. Each of the
classifications returned were a fair description of the image.

If you wanted to restrict the results to those that are at least 90% confident
or greater, you would simply adjust the `threshold` in the
`visual_recognition.classify()` function.

When your program runs, it should show the output below for each photo you
provide.

```txt
----------------------------------------------------------------
Image Title:  Grizzly Bear
Image URL: https://example.com/photos/20140717.jpg

brown bear ( 0.944 )
bear ( 1 )
carnivore ( 1 )
mammal ( 1 )
animal ( 1 )
Alaskan brown bear ( 0.759 )
greenishness color ( 0.975 )
----------------------------------------------------------------
```

## Discussion

Now, this was a very minimal implementation of the API. We simply supplied some
images and looked to see how accurate the results were. However, you could
implement this type of API into many machine learning (ML) models.

For example, you could be working for a company that scans their warehouses or
inventory using drones. Would you want to pay employees to sit there and watch
drone footage all day in order to identify or count things in the video?
Probably not. Instead, you could use a classification system similar to this one
in order to train your machine learning model to correctly identify items that
the drones show through video. More specifically, you could have your machine
learning model watch a drone fly over a field of sheep in order to count how
many sheep are living in that field.

There are many ways to implement machine learning functionality, but hopefully
this post helped inspire some deeper thought about the tools that can help
propel us further into the future of machine learning and AI.
