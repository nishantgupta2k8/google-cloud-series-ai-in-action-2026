# google-cloud-series-ai-in-action-2026


# lab-02-generate-and-edit-images-with-nano-banana

GENAI131
Overview
In this lab, you will explore the image generation and editing capabilities of the new Gemini Flash Image model, often referred to by its catchy codename: Nano Banana.

This model produces text and image outputs, and is quite popular for the degree of control it offers by allowing you to edit images by describing the changes you would like to see. Learn more about its capabilities from the Gemini Flash Image model card.

Objectives
In this lab, you use the Google Gen AI SDK for Python to interact with Nano Banana to:

Generate images using the Vertex AI Studio UI.
Generate photorealistic images from code by using other images as inputs.
Generate non-photo images with text based on other images.
Setup and requirements
Before you click the Start Lab button
Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Google Cloud resources will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

What you need
To complete this lab, you need:

Access to a standard internet browser (Chrome browser recommended).
Time to complete the lab.
Note: If you already have your own personal Google Cloud account or project, do not use it for this lab.

Note: If you are using a Pixelbook, open an Incognito window to run this lab.

How to start your lab and sign in to the Google Cloud console
Click the Start Lab button. If you need to pay for the lab, a dialog opens for you to select your payment method. On the left is the Lab Details pane with the following:

The Open Google Cloud console button
Time remaining
The temporary credentials that you must use for this lab
Other information, if needed, to step through this lab
Click Open Google Cloud console (or right-click and select Open Link in Incognito Window if you are running the Chrome browser).

The lab spins up resources, and then opens another tab that shows the Sign in page.

Tip: Arrange the tabs in separate windows, side-by-side.

Note: If you see the Choose an account dialog, click Use Another Account.
If necessary, copy the Username below and paste it into the Sign in dialog.

"Username"
Copied!
You can also find the Username in the Lab Details pane.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

"Password"
Copied!
You can also find the Password in the Lab Details pane.

Click Next.

Important: You must use the credentials the lab provides you. Do not use your Google Cloud account credentials.
Note: Using your own Google Cloud account for this lab may incur extra charges.
Click through the subsequent pages:

Accept the terms and conditions.
Do not add recovery options or two-factor authentication (because this is a temporary account).
Do not sign up for free trials.
After a few moments, the Google Cloud console opens in this tab.

Note: To access Google Cloud products and services, click the Navigation menu or type the service or product name in the Search field. Navigation menu icon and Search field
Task 1. Explore Nano Banana in the Vertex AI Studio UI
In this section, you will use the Vertex AI Studio UI to explore Nano Banana.

In the Google Cloud console, from the Navigation menu (Navigation menu), select Vertex AI > Vertex AI Studio.

In the chat input, type /model and press return to bring up the model settings.

Select Nano Banana (gemini-flash-image-model-id) from the Gemini tab

In the prompt input box, click the + sign to add media and select Import from Cloud Storage.

Select the bucket gs://YOUR_GCP_PROJECT_ID-bucket/ and the image cymbal-shops-ui.png.

Click Select.

Click the image that has appeared in the prompt to see a larger resolution version of the image. It is a UI of a new mobile website for Cymbal Shops.

Select the image in the prompt
Close the detailed image view.

Enter the following text to accompany the image, then click the Submit arrow button.

Create a closeup photo featuring a young couple from Thiruvananthapuram in a kitchen cooking Kerala Parotta and looking at this UI on a tablet screen. They should be holding the tablet in such a way that we can see the UI on the screen.
Copied!
Example Output (yours may be different):

A couple in TVM viewing your UI
Note: Not every image generated will be exactly as expected, but you can re-run generations to see different results.
Follow up with the following prompt to see how Nano Banana can maintain consistency across images within a chat session:

Show the same couple opening their own restaurant.
Copied!
Example Output (yours may be different):

The couple opening their restaurant
You can click New chat in the upper left of the studio and try the prompt again to see variations.

Click Check my progress to verify the objectives.
Explore Nano Banana in the Vertex AI Studio UI.

Task 2. Set up a Colab Enterprise notebook environment
In this section, you will open a Colab Enterprise notebook to send requests to the Nano Banana model using the Python Google Gen AI SDK.

In the Google Cloud console, from the Navigation menu (Navigation menu), select Vertex AI > Colab Enterprise.

Click Create notebook.

When the notebook has been created, use the File > Rename menu item to rename the notebook to:

nano-banana.ipynb
Copied!
Click the Connect button in the upper right of the notebook UI to create a runtime environment and connect to it.

Follow the prompts to authorize your notebook as your Qwiklabs student account.

Creating the runtime will take a few minutes. When it is connected, you will notice this runtime monitoring display in the upper right.

RAM and disk status display
You can dismiss any pop-up guides or notifications that appear.

When the runtime has become active, paste the following into the first cell, and run the cell by pressing Shift + Enter on your keyboard or selecting the play button to the left of the cell.

!pip install --upgrade --quiet google-genai
Copied!
NOTE: Ignore any compatibility errors during installation of the libraries.
When the code cell has finished running (indicated by a number in brackets ([1]) appearing to the left of the code cell), restart the kernel by using the menus to select Runtime > Restart session and selecting Yes.

Paste the following into the next code cell to import packages and do some setup. Run the cell with Shift + Enter or by pressing the play icon to the left of the cell:

from io import BytesIO
from PIL import Image
from google.cloud import storage
from google import genai
from google.genai import types

storage_client = storage.Client()

PROJECT_ID = "YOUR_GCP_PROJECT_ID"
BUCKET_NAME = f"{PROJECT_ID}-bucket"
NANO_BANANA = "gemini-flash-image-model-id | disablehighlight"

client = genai.Client(
    vertexai=True,
    project=PROJECT_ID,
    location="global",
)
Copied!
In the next cell, paste and run the following to define helper functions to display images in your notebook:

def display_image(image_data):
    """Displays an image in a Colab notebook from PNG data."""
    img = Image.open(BytesIO(image_data))
    img.save('my_image.png')
    display(img)

def display_img_from_gcs(bucket_name, img_path):
    """Downloads bytes and displays an image stored in GCS."""
    bucket = storage_client.get_bucket(bucket_name)
    blob = bucket.get_blob(img_path)
    display_image(blob.download_as_bytes())
Copied!
Click Check my progress to verify the objectives.
Set up the Colab Enterprise notebook environment.

Task 3. Query Nano Banana from your notebook
In a new cell, paste and run the following to preview an image of a jacket product you will work with:

display_img_from_gcs(BUCKET_NAME, "product_jacket_932.png")
Copied!
In a new cell, paste and run the following to view another product image you will work with -- this time it's a dress:

display_img_from_gcs(BUCKET_NAME, "product_dress_128.png")
Copied!
To send these to Gemini, paste and run the following to create a genai.types.Part for each image and a text prompt, then combine them into genai.types.Content identified as coming from the user that we can send to the model:

image1 = types.Part.from_uri(
    file_uri=f"gs://{BUCKET_NAME}/product_dress_128.png",
    mime_type="image/png",
)

image2 = types.Part.from_uri(
    file_uri=f"gs://{BUCKET_NAME}/product_jacket_932.png",
    mime_type="image/png",
)

prompt = types.Part.from_text(
    text=("Create a photo featuring a young couple"
          "from Austin, TX wearing these clothing"
          "products on a walk through the Greenbelt.")
)

content = [
    types.Content(
        role="user",
        parts=[image1, image2, prompt]
    )
]
Copied!
To use Nano Banana, you also need to specify that you expect a response including text and images. You do that by setting a genai.types.GenerateContentConfig with response_modalities set to ["TEXT", "IMAGE"]. Retry optins are also a good idea to include to handle resource errors due to the popularity of the model. Paste and run this code to create this configuration:

gen_config = types.GenerateContentConfig(
    response_modalities = ["TEXT", "IMAGE"],
    http_options = types.HttpOptions(
        retry_options = types.HttpRetryOptions(
            attempts = 60,
            initial_delay = 1,
            exp_base = 2,
            max_delay = 4
        )
    )
)
Copied!
Now you can paste and run the following code to make a call to generate_content and then use one of the helper functions you defined earlier to display the image:

response = client.models.generate_content(
    model = NANO_BANANA,
    contents = content,
    config = gen_config,
    )

for part in response.candidates[0].content.parts:
    if part.inline_data:
        image_data = part.inline_data.data
        display_image(image_data)
Copied!
Example Output (yours may be different):

A couple in Austin wearing the products.
Click Check my progress to verify the objectives.
Query Nano Banana from your notebook.

Task 4. Use Nano Banana to generate images that include text
At the bottom of the notebook, mouse-over the space at the end of a cell and select the + Text button to add a Markdown text cell.

Add a text cell
Paste the following Markdown text and run the cell to render Markdown text, which can be useful to define structure in your notebook.

## Edit a UI with Nano Banana
Copied!
A new code cell will have been created for you after you have run the Markdown cell.

In the new code cell, paste and run the following code to display another image you will edit using Nano Banana.

This UI comes from the Google Cloud console -- specifically the Vertex AI Studio's Media Studio UI for music generation with the Lyria model.

display_img_from_gcs(BUCKET_NAME, "lyria_ui.png")
Copied!
In a new code cell, paste and run the following code to generate a UI design in the same style, but with the purpose of generating podcasts.

ui_img_part = types.Part.from_uri(
    file_uri=f"gs://{BUCKET_NAME}/lyria_ui.png",
    mime_type="image/png",
)
text1 = types.Part.from_text(
    text="""
    Create a UI in the same style.
    It should be called Podcast Studio.
    Include configuration options:
    - A UI slider to set the number of hosts
      (from 1 to max 4)
    - A UI slider to set the minutes in length
      (from 2 to max 40)
    The prompt input box should tell the user to
    specify details of the episode topic.
    """
)

contents = [
    types.Content(
        role="user",
        parts=[ui_img_part, text1]
    )
]

response = client.models.generate_content(
    model = NANO_BANANA,
    contents = contents,
    config = gen_config,
    )

for part in response.candidates[0].content.parts:
    if part.inline_data:
        image_data = part.inline_data.data
        display_image(image_data)
Copied!
In a new code cell, paste and run the following code to upload and edit the previous image, replacing the "Advanced Options" section with a new configuration option to set the podcast's language:

bucket = storage_client.get_bucket(BUCKET_NAME)
blob = bucket.blob("original_ui.png")
blob.upload_from_filename("my_image.png")

original_ui_img_part = types.Part.from_uri(
    file_uri=f"gs://{BUCKET_NAME}/original_ui.png",
    mime_type="image/png",
)
text = types.Part.from_text(
    text="""
        Modify this image to remove the
        "Advanced Options" area. Keep current
        configuration options (Number of hosts
        and Minutes in Length) and add a new
        dropdown for Podcast Language.
    """
)

contents = [
    types.Content(
        role="user",
        parts=[original_ui_img_part, text]
    )
]

response = client.models.generate_content(
    model = NANO_BANANA,
    contents = contents,
    config = gen_config,
    )

for part in response.candidates[0].content.parts:
    if part.inline_data:
        image_data = part.inline_data.data
        display_image(image_data)
Copied!
Example Output (yours may be different):

Example output showing a Podcast Studio UI
Click Check my progress to verify the objectives.
Generate images that include text using Nano Banana.

Optional: Keep Experimenting
You have now used Nano Banana from both the Vertex AI Studio and the Python SDK. This is a great time to experiment on your own and build more intuition for the model's capabilities. This section is not graded, so feel free to explore! Here are a few ideas:

In your Colab Notebook:

Try modifying the prompts in Task 3. What if the couple is in a different city or doing a different activity?
In Task 4, try changing the UI prompt to design a different kind of application. What about a weather app or a music player in the same style?
In Vertex AI Studio:

Return to the Studio (from Task 1) and open a New chat.
Try uploading the product or UI images from the lab (like product_jacket_932.png or lyria_ui.png) and use text prompts to generate new scenes or edits.
See if you can use follow-up prompts to refine your generated images.



GENAI131
Overview
In this lab, you will explore the image generation and editing capabilities of the new Gemini 2.5 Flash Image model, often referred to by its catchy codename: Nano Banana.

This model produces text and image outputs, and is quite popular for the degree of control it offers by allowing you to edit images by describing the changes you would like to see. Learn more about its capabilities from the Gemini 2.5 Flash Image model card.

Objectives
In this lab, you use the Google Gen AI SDK for Python to interact with Nano Banana to:

Generate images using the Vertex AI Studio UI.
Generate photorealistic images from code by using other images as inputs.
Generate non-photo images with text based on other images.
Setup and requirements
Before you click the Start Lab button
Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Google Cloud resources will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

What you need
To complete this lab, you need:

Access to a standard internet browser (Chrome browser recommended).
Time to complete the lab.
Note: If you already have your own personal Google Cloud account or project, do not use it for this lab.

Note: If you are using a Pixelbook, open an Incognito window to run this lab.

How to start your lab and sign in to the Google Cloud console
Click the Start Lab button. If you need to pay for the lab, a dialog opens for you to select your payment method. On the left is the Lab Details pane with the following:

The Open Google Cloud console button
Time remaining
The temporary credentials that you must use for this lab
Other information, if needed, to step through this lab
Click Open Google Cloud console (or right-click and select Open Link in Incognito Window if you are running the Chrome browser).

The lab spins up resources, and then opens another tab that shows the Sign in page.

Tip: Arrange the tabs in separate windows, side-by-side.

Note: If you see the Choose an account dialog, click Use Another Account.
If necessary, copy the Username below and paste it into the Sign in dialog.

student-01-88c31f500d29@qwiklabs.net
Copied!
You can also find the Username in the Lab Details pane.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

VhO9F4uso0ca
Copied!
You can also find the Password in the Lab Details pane.

Click Next.

Important: You must use the credentials the lab provides you. Do not use your Google Cloud account credentials.
Note: Using your own Google Cloud account for this lab may incur extra charges.
Click through the subsequent pages:

Accept the terms and conditions.
Do not add recovery options or two-factor authentication (because this is a temporary account).
Do not sign up for free trials.
After a few moments, the Google Cloud console opens in this tab.

Note: To access Google Cloud products and services, click the Navigation menu or type the service or product name in the Search field. Navigation menu icon and Search field
Task 1. Explore Nano Banana in the Vertex AI Studio UI
In this section, you will use the Vertex AI Studio UI to explore Nano Banana.

In the Google Cloud console, from the Navigation menu (Navigation menu), select Vertex AI > Vertex AI Studio.

In the chat input, type /model and press return to bring up the model settings.

Select Nano Banana (gemini-2.5-flash-image) from the Gemini tab

In the prompt input box, click the + sign to add media and select Import from Cloud Storage.

Select the bucket gs://qwiklabs-gcp-01-55520ef820f4-bucket/ and the image cymbal-shops-ui.png.

Click Select.

Click the image that has appeared in the prompt to see a larger resolution version of the image. It is a UI of a new mobile website for Cymbal Shops.

Select the image in the prompt
Close the detailed image view.

Enter the following text to accompany the image, then click the Submit arrow button.

Create a closeup photo featuring a young couple from Thiruvananthapuram in a kitchen cooking Kerala Parotta and looking at this UI on a tablet screen. They should be holding the tablet in such a way that we can see the UI on the screen.
Copied!
Example Output (yours may be different):

A couple in TVM viewing your UI
Note: Not every image generated will be exactly as expected, but you can re-run generations to see different results.
Follow up with the following prompt to see how Nano Banana can maintain consistency across images within a chat session:

Show the same couple opening their own restaurant.
Copied!
Example Output (yours may be different):

The couple opening their restaurant
You can click New chat in the upper left of the studio and try the prompt again to see variations.

Click Check my progress to verify the objectives.
Assessment Completed!
Explore Nano Banana in the Vertex AI Studio UI.
Assessment Completed!

Task 2. Set up a Colab Enterprise notebook environment
In this section, you will open a Colab Enterprise notebook to send requests to the Nano Banana model using the Python Google Gen AI SDK.

In the Google Cloud console, from the Navigation menu (Navigation menu), select Vertex AI > Colab Enterprise.

Click Create notebook.

When the notebook has been created, use the File > Rename menu item to rename the notebook to:

nano-banana.ipynb
Copied!
Click the Connect button in the upper right of the notebook UI to create a runtime environment and connect to it.

Follow the prompts to authorize your notebook as your Qwiklabs student account.

Creating the runtime will take a few minutes. When it is connected, you will notice this runtime monitoring display in the upper right.

RAM and disk status display
You can dismiss any pop-up guides or notifications that appear.

When the runtime has become active, paste the following into the first cell, and run the cell by pressing Shift + Enter on your keyboard or selecting the play button to the left of the cell.

!pip install --upgrade --quiet google-genai
Copied!
NOTE: Ignore any compatibility errors during installation of the libraries.
When the code cell has finished running (indicated by a number in brackets ([1]) appearing to the left of the code cell), restart the kernel by using the menus to select Runtime > Restart session and selecting Yes.

Paste the following into the next code cell to import packages and do some setup. Run the cell with Shift + Enter or by pressing the play icon to the left of the cell:

from io import BytesIO
from PIL import Image
from google.cloud import storage
from google import genai
from google.genai import types

storage_client = storage.Client()

PROJECT_ID = "qwiklabs-gcp-01-55520ef820f4"
BUCKET_NAME = f"{PROJECT_ID}-bucket"
NANO_BANANA = "gemini-2.5-flash-image"

client = genai.Client(
    vertexai=True,
    project=PROJECT_ID,
    location="global",
)
Copied!
In the next cell, paste and run the following to define helper functions to display images in your notebook:

def display_image(image_data):
    """Displays an image in a Colab notebook from PNG data."""
    img = Image.open(BytesIO(image_data))
    img.save('my_image.png')
    display(img)

def display_img_from_gcs(bucket_name, img_path):
    """Downloads bytes and displays an image stored in GCS."""
    bucket = storage_client.get_bucket(bucket_name)
    blob = bucket.get_blob(img_path)
    display_image(blob.download_as_bytes())
Copied!
Click Check my progress to verify the objectives.
Assessment Completed!
Set up the Colab Enterprise notebook environment.
Assessment Completed!

Task 3. Query Nano Banana from your notebook
In a new cell, paste and run the following to preview an image of a jacket product you will work with:

display_img_from_gcs(BUCKET_NAME, "product_jacket_932.png")
Copied!
In a new cell, paste and run the following to view another product image you will work with -- this time it's a dress:

display_img_from_gcs(BUCKET_NAME, "product_dress_128.png")
Copied!
To send these to Gemini, paste and run the following to create a genai.types.Part for each image and a text prompt, then combine them into genai.types.Content identified as coming from the user that we can send to the model:

image1 = types.Part.from_uri(
    file_uri=f"gs://{BUCKET_NAME}/product_dress_128.png",
    mime_type="image/png",
)

image2 = types.Part.from_uri(
    file_uri=f"gs://{BUCKET_NAME}/product_jacket_932.png",
    mime_type="image/png",
)

prompt = types.Part.from_text(
    text=("Create a photo featuring a young couple"
          "from Austin, TX wearing these clothing"
          "products on a walk through the Greenbelt.")
)

content = [
    types.Content(
        role="user",
        parts=[image1, image2, prompt]
    )
]
Copied!
To use Nano Banana, you also need to specify that you expect a response including text and images. You do that by setting a genai.types.GenerateContentConfig with response_modalities set to ["TEXT", "IMAGE"]. Retry optins are also a good idea to include to handle resource errors due to the popularity of the model. Paste and run this code to create this configuration:

gen_config = types.GenerateContentConfig(
    response_modalities = ["TEXT", "IMAGE"],
    http_options = types.HttpOptions(
        retry_options = types.HttpRetryOptions(
            attempts = 60,
            initial_delay = 1,
            exp_base = 2,
            max_delay = 4
        )
    )
)
Copied!
Now you can paste and run the following code to make a call to generate_content and then use one of the helper functions you defined earlier to display the image:

response = client.models.generate_content(
    model = NANO_BANANA,
    contents = content,
    config = gen_config,
    )

for part in response.candidates[0].content.parts:
    if part.inline_data:
        image_data = part.inline_data.data
        display_image(image_data)
Copied!
Example Output (yours may be different):

A couple in Austin wearing the products.
Click Check my progress to verify the objectives.
Assessment Completed!
Query Nano Banana from your notebook.
Assessment Completed!

Task 4. Use Nano Banana to generate images that include text
At the bottom of the notebook, mouse-over the space at the end of a cell and select the + Text button to add a Markdown text cell.

Add a text cell
Paste the following Markdown text and run the cell to render Markdown text, which can be useful to define structure in your notebook.

## Edit a UI with Nano Banana
Copied!
A new code cell will have been created for you after you have run the Markdown cell.

In the new code cell, paste and run the following code to display another image you will edit using Nano Banana.

This UI comes from the Google Cloud console -- specifically the Vertex AI Studio's Media Studio UI for music generation with the Lyria model.

display_img_from_gcs(BUCKET_NAME, "lyria_ui.png")
Copied!
In a new code cell, paste and run the following code to generate a UI design in the same style, but with the purpose of generating podcasts.

ui_img_part = types.Part.from_uri(
    file_uri=f"gs://{BUCKET_NAME}/lyria_ui.png",
    mime_type="image/png",
)
text1 = types.Part.from_text(
    text="""
    Create a UI in the same style.
    It should be called Podcast Studio.
    Include configuration options:
    - A UI slider to set the number of hosts
      (from 1 to max 4)
    - A UI slider to set the minutes in length
      (from 2 to max 40)
    The prompt input box should tell the user to
    specify details of the episode topic.
    """
)

contents = [
    types.Content(
        role="user",
        parts=[ui_img_part, text1]
    )
]

response = client.models.generate_content(
    model = NANO_BANANA,
    contents = contents,
    config = gen_config,
    )

for part in response.candidates[0].content.parts:
    if part.inline_data:
        image_data = part.inline_data.data
        display_image(image_data)
Copied!
In a new code cell, paste and run the following code to upload and edit the previous image, replacing the "Advanced Options" section with a new configuration option to set the podcast's language:

bucket = storage_client.get_bucket(BUCKET_NAME)
blob = bucket.blob("original_ui.png")
blob.upload_from_filename("my_image.png")

original_ui_img_part = types.Part.from_uri(
    file_uri=f"gs://{BUCKET_NAME}/original_ui.png",
    mime_type="image/png",
)
text = types.Part.from_text(
    text="""
        Modify this image to remove the
        "Advanced Options" area. Keep current
        configuration options (Number of hosts
        and Minutes in Length) and add a new
        dropdown for Podcast Language.
    """
)

contents = [
    types.Content(
        role="user",
        parts=[original_ui_img_part, text]
    )
]

response = client.models.generate_content(
    model = NANO_BANANA,
    contents = contents,
    config = gen_config,
    )

for part in response.candidates[0].content.parts:
    if part.inline_data:
        image_data = part.inline_data.data
        display_image(image_data)
Copied!
Example Output (yours may be different):

Example output showing a Podcast Studio UI
Click Check my progress to verify the objectives.
Assessment Completed!
Generate images that include text using Nano Banana.
Assessment Completed!

Optional: Keep Experimenting
You have now used Nano Banana from both the Vertex AI Studio and the Python SDK. This is a great time to experiment on your own and build more intuition for the model's capabilities. This section is not graded, so feel free to explore! Here are a few ideas:

In your Colab Notebook:

Try modifying the prompts in Task 3. What if the couple is in a different city or doing a different activity?
In Task 4, try changing the UI prompt to design a different kind of application. What about a weather app or a music player in the same style?
In Vertex AI Studio:

Return to the Studio (from Task 1) and open a New chat.
Try uploading the product or UI images from the lab (like product_jacket_932.png or lyria_ui.png) and use text prompts to generate new scenes or edits.
See if you can use follow-up prompts to refine your generated images.





lab-03-intro-to-gemini-enterprise

GSP1320
Google Cloud self-paced labs logo

Overview
Gemini Enterprise unlocks enterprise expertise for employees with agents that bring together Gemini's advanced reasoning, Google-quality search, and enterprise data, regardless of where it's hosted. It empowers employees to find the right information at the right time by connecting content scattered across an enterprise, generating grounded, personalized answers, and performing tasks through integrated workflow actions. Featuring AI agents that can plan, reason, and execute tasks, Gemini Enterprise assists with information retrieval and summarization, task automation, data analysis, and reporting.

Business use-case
For Cymbal Foods, a food manufacturing and distribution company, Gemini Enterprise offers a solution to their significant data fragmentation and information silo challenges. Currently, Cymbal Foods struggles with inaccurate demand forecasting, leading to food waste and financial losses. This makes it incredibly difficult for employees to find information, gain actionable insights, or collaborate effectively. By implementing Gemini Enterprise, Cymbal Foods can connect these disparate data sources. This allows employees, from production managers to data analysts, to easily search across all systems, retrieve and summarize sales data, customer feedback, and inventory levels, and gain a holistic view of operations.

In this lab, you will deploy a Gemini Enterprise application and connect it to diverse data stores including Google Cloud Storage, Google Drive, and Google Calendar. You will then explore its capabilities, from using the general AI assistant for information discovery to creating custom agents for specific tasks and leveraging NotebookLM for in-depth content analysis.

Objectives
In this lab, you learn how to:

Create Google Drive, Cloud Storage, and Google Calendar data stores.
Create a Gemini Enterprise app.
Use Gemini Enterprise agents to find, summarize, and extract content from different data stores.
Create a deep research report agent.
Create a multi-agent ideation session.
Utilize NotebookLM for focused, document-grounded analysis and generate an audio overview of curated sources.
Setup and requirements
Before you click the Start Lab button
Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Google Cloud resources are made available to you.

This hands-on lab lets you do the lab activities in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials you use to sign in and access Google Cloud for the duration of the lab.

To complete this lab, you need:

Access to a standard internet browser (Chrome browser recommended).
Note: Use an Incognito (recommended) or private browser window to run this lab. This prevents conflicts between your personal account and the student account, which may cause extra charges incurred to your personal account.
Time to complete the lab—remember, once you start, you cannot pause a lab.
Note: Use only the student account for this lab. If you use a different Google Cloud account, you may incur charges to that account.
How to start your lab and sign in to the Google Cloud console
Click the Start Lab button. If you need to pay for the lab, a dialog opens for you to select your payment method. On the left is the Lab Details pane with the following:

The Open Google Cloud console button
Time remaining
The temporary credentials that you must use for this lab
Other information, if needed, to step through this lab
Click Open Google Cloud console (or right-click and select Open Link in Incognito Window if you are running the Chrome browser).

The lab spins up resources, and then opens another tab that shows the Sign in page.

Tip: Arrange the tabs in separate windows, side-by-side.

Note: If you see the Choose an account dialog, click Use Another Account.
If necessary, copy the Username below and paste it into the Sign in dialog.

"Username"
Copied!
You can also find the Username in the Lab Details pane.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

"Password"
Copied!
You can also find the Password in the Lab Details pane.

Click Next.

Important: You must use the credentials the lab provides you. Do not use your Google Cloud account credentials.
Note: Using your own Google Cloud account for this lab may incur extra charges.
Click through the subsequent pages:

Accept the terms and conditions.
Do not add recovery options or two-factor authentication (because this is a temporary account).
Do not sign up for free trials.
After a few moments, the Google Cloud console opens in this tab.

Note: To access Google Cloud products and services, click the Navigation menu or type the service or product name in the Search field. Navigation menu icon and Search field
Task 1. Prepare data for Gemini Enterprise, NotebookLM, and Calendar
In this first task, you will source sample documents from Google Cloud Storage (GCS) that have been prepared for your lab environment. You will then organize these files within your Google Drive into distinct folders, preparing them for use with different tools: one set for the broader Gemini Enterprise application and its custom agents, and another text-based set specifically for focused analysis in NotebookLM later in the lab. Finally, you'll add a relevant meeting to your Google Calendar to create diverse content for Gemini Enterprise to interact with.

From the Google Cloud Console Navigation menu (☰), navigate to Cloud Storage > Buckets.

Click on the name of the GCS bucket that has been pre-provisioned for you in this lab. The bucket name is your Project ID: GCS Bucket.

Inside your bucket, you should see at least three folders prepared by the startup script:

gemini-enterprise-drive/: Contains CSV files and images intended for use with your Gemini Enterprise application and custom agents that work directly with these formats.
notebooklm-drive/: Contains plain text (.txt) versions of key data files, optimized for use with NotebookLM.
gemini-enterprise-cloud-storage/: Contains various other data files that you will connect to Gemini Enterprise via a Cloud Storage data store in a later task.
For this task, you will focus on the contents of gemini-enterprise-drive/ and notebooklm-drive/ to populate your Google Drive.

Stage files for the Gemini Enterprise application in Google Drive
First, navigate into the gemini-enterprise-drive/ folder within your GCS bucket.

Download all the files listed below from this folder to your local system. These will be used by your main Gemini Enterprise application.

MARKETING_data.csv
SALES_data.csv
customer_feedback.csv
pie_chart_customer_sentiment-png.png
Note: If using the Cloud Storage UI to download, you may need to download each file individually. When downloading the PNG file, you will need to use your browser's "Save Page As..." option, typically by pressing Ctrl+S (on Windows/Linux) or Cmd+S (on Mac).
In the Incognito window where you have logged into the Google Cloud console, open a new browser tab and navigate to your Google Drive at drive.google.com. Ensure you are logged in with your Qwiklabs student account.

In Google Drive, create a new folder and name it Gemini Enterprise Drive Assets. This folder will hold the data primarily used by your Gemini Enterprise application.

Upload the .csv files and the .png file you just downloaded from the gemini-enterprise-drive/ GCS folder into this newly created Gemini Enterprise Drive Assets Google Drive folder. Your folder should resemble the following (containing the four files):

Gemini Enterprise Drive Assets in Drive

Download text files for NotebookLM
Now, go back to your Cloud Storage browser tab. Navigate up one level from the gemini-enterprise-drive/ folder (if needed) and then navigate into the notebooklm-drive/ folder.

Download all the plain text (.txt) files listed below from this folder to your local system. These files are specifically prepared for use with NotebookLM, and you will use them in a later task.

MARKETING_data.txt
SALES_data.txt
customer_feedback.txt
Important Note on Downloading .txt Files: When you click on a .txt file link in the Google Cloud Storage browser UI, your browser might open and display the content as a plain text page instead of directly downloading the file.

If this happens:
To save the file, use your browser's "Save Page As..." option, typically by pressing Ctrl+S (on Windows/Linux) or Cmd+S (on Mac).
Ensure you save the file with its original name and the .txt extension (e.g., SALES_data.txt).
Add an event to Google Calendar
In the same Incognito window, open another new tab and navigate to Google Calendar at calendar.google.com. Accept or dismiss any pop-up messages.

Ensure you are authenticated with your Qwiklabs student account by clicking the circular profile icon (it might show an "s") in the upper right corner and confirming it is your student-...@qwiklabs.net account.

In the upper-left corner of Google Calendar, click + Create and select Event from the dropdown menu.

In the event creation window that appears:

Set the event title to: Cymbal Foods - Marketing Strategy Meeting.
Set a start time that is at least one hour from the current time.
(Optional) Add any other relevant details if you wish.
Click Save.

Click Check my progress to verify the objective.
Add content to Google Drive and Google Calendar.

Task 2. Create the Gemini Enterprise application
In this section, you will perform the initial one-time configuration for Gemini Enterprise within your enterprise environment by creating the application and setting up Google Identity as the identity provider.

Configure Gemini Enterprise
In the Google Cloud Console, navigate to the Gemini Enterprise landing page by searching for it at the top of the console and clicking on it.

On the Gemini Enterprise card, click Start 30 Day Free Trial.

Gemini Enterprise landing page

You will be prompted to enable the Gemini Code Assist API, click Continue and Activate the API.

Once you've been redirected to the Create page, set the App name to Cymbal Foods - Gemini Enterprise.

For Location, select global.

Click Create.

Your Gemini Enterprise app has been created. It should resemble the following:

Gemini Enterprise app created

Click Set up identity.

Select Use Google Identity, then click Confirm Workforce Identity.

Click Check my progress to verify the objective.
Configure an AI Applications identity provider.

Task 3. Connect to your data stores
In this section, you will define the information sources for your Gemini Enterprise application by creating data stores. These are essential connections that enable Gemini Enterprise to access, index, and make searchable the data from systems like Google Drive, Cloud Storage, and Google Calendar, providing the AI assistant with the content it needs to operate. You will create three specific data stores: one for Google Drive, one for Cloud Storage, and one for Google Calendar.

Create a Google Drive data store
From the Gemini Enterprise Applications menu, select Connected data stores from the left-hand navigation pane.

Select New Data Store.

Locate the Google Drive card and click Select.

For Choose drives you want to sync, select All.

Note: While Gemini Enterprise makes it easier to find information across your data sources, it does not grant additional access. You will still only have access to the documents that have been shared with you via existing Drive permissions, and others will only have access to your documents that you have shared with them.
Click Continue.

Keep the default region of global, and give the data store the name Google Drive.

If you've asked for billing, leave the pricing model for billing as default, and click Continue.

Select Create.

Create a Google Calendar data store
Follow the steps in the previous task again, only this time create a data store for Google Calendar. In the Action tab, select Skip, and name the data connector Google Calendar.
Create a Cloud Storage data store
From the Gemini Enterprise Applications menu, select Connected data stores from the left-hand navigation pane.

Select New Data Store.

Locate the Cloud Storage card and click Select.

For the data type, select Documents from the Unstructured Data Import (Document Search & RAG) section.

For Synchronization frequency, select One time.

Lastly, select the gemini-enterprise-cloud-storage folder in your Cloud Storage bucket by selecting Browse then navigating to that subfolder within your GCP Bucket bucket.

Click Continue.

Keep the default region of global, and give the data store the name Cloud Storage.

If you've asked for billing, leave the pricing model for billing as default, and click Continue.

Select Create.

The three data stores should be listed in the Gemini Enterprise > Connected data stores panel.

Listed data stores

Click Check my progress to verify the objective.
Create Google Drive, Calendar, and Cloud Storage data stores.

Task 4. Interact with your Gemini Enterprise agents
Now that your Cymbal Foods Gemini Enterprise application is fully configured and connected to your data, it's time to experience its capabilities firsthand. In this task, you will interact directly with agents, testing their ability to search across your uploaded documents, extract specific information, summarize content, and even perform actions like multi-agent ideation. This hands-on exploration will showcase how Gemini Enterprise can transform information retrieval and task management within an organization.

Note: it may take 5-10 minutes for your data stores to fully index. If you don't see the sources populated on the Gemini Enterprise application, please wait a few minutes and try again.
From the Gemini Enterprise page, click the Overview tab.

Select Go to Gemini Enterprise to open the application. Note that this will look different depending on the data stores used. Your home page should resemble the following:

Gemini Enterprise UI

This home page is designed to give users easy access to the content and events they will find most useful. You will see a few sections:

Agents: Use some Google-provided agents or create your own to help with tasks like research, iterating on ideas, or more.
Idea Generation: Create a multi-agent ideation session with a team of agents.
NotebookLM: an AI-powered research and writing assistant that works best with the sources you upload.
Deep Research: an agentic feature in Gemini that can automatically browse up to hundreds of websites on your behalf, think through its findings, and create insightful multi-page reports in minutes.
Note: If your data stores are still indexing, the files may require a little time to become accessible.
In the search bar, enter the following prompt:

Give me an update on our latest marketing data.
Copied!
Your answer should resemble the following:

Search Result 1

Note:If no response is received, please refresh and submit the query again.
As a followup, ask:
What products were in the March newsletter?
Copied!
You can use the Gemini Enterprise AI assistant to handle multimodal prompts as well.

Click the New chat button on the top left corner to start a new chat.

In the search bar, click the Tools button, and select Search Company data.

Enter the following prompt:

Summarize the customer sentiment pie chart.
Copied!
The response should resemble the following:

pie chart summarized by AI assistant

Note: you may need to run the query again if it doesn't find the pie chart image file you uploaded.
Click the New chat button on the top left corner to start a new chat.

Enter the following prompt:

What meetings are on my agenda today? What do I need to have prepped for them?
Copied!
Observe the response. Since you set up the data stores for your Google Calendar, it knows what events you have, and can assist you in curating the information you need for them!

Multi-agent ideation
In this section, you will use the Idea Generation agent to create a multi-agent ideation session for your sales presentation.

Click the Google Gemini Enterprise logo to return to the homepage.

Click Idea Generation.

In the Idea Generation prompt, enter:

Help me brainstorm a new marketing campaign for Gen-Z customers based on our latest products. 
Copied!
You should see a high level presentation proposal generated for you:

presentation flow for gen-z marketing campaign

Review the proposal, scroll down, and click Start session to start generating ideas.
Your session has been started and your team of AI agents is hard at work cranking out ideas! You can view and interact with your session at any time, but note that it might take a few minutes for the first ideas to be ready to view.

photo of agent ideation session

While you wait for the ideation session to finish in the background, you can carry on with the next steps in the lab, and come back to the results later.

Click Check my progress to verify the objective.
Interact with your Gemini Enterprise agents.

Task 5. Use the Deep Research agent
In this section, you will use the Deep Research agent to unlock deep insights with AI-driven research, reports, and audio summaries.

Click the Google Gemini Enterprise logo to return to the homepage.

On the left-hand navigation menu, select Deep Research.

Enter the following prompt:

Research the latest marketing tips and tricks that food companies are using, and generate a list of actionable steps our company can take to improve our current marketing strategy.
Copied!
The agent will come up with a research plan for you.

Scroll down, and click Start Research to begin the task.
Note: since Cymbal Foods is a fictitious company, the internal data of the current marketing strategy will not be real or accurate. The goal of this task is to show the usefulness and capabilities of the Deep Research agent.
This will take a few minutes to complete. You can choose to wait or continue on with the tasks in the lab, and come back to it later to check on the status of the generated report. After 5-10 minutes, your research report will be completed, and you will see the following summary and audio overview!

deep research report completed

Task 6. Focused analysis with NotebookLM
In the previous tasks, you explored Gemini Enterprise as a comprehensive platform for accessing enterprise information and utilizing specialized AI agents. Now, you'll delve into NotebookLM, a tool designed for focused research, deep analysis, and sense-making with specific collections of documents. NotebookLM acts as your personal research assistant, allowing you to upload your chosen sources and then ask detailed questions, generate summaries, and synthesize information, with all AI responses grounded directly in the materials you provide.

For Cymbal Foods, an employee might use NotebookLM to conduct an in-depth review of specific sales reports alongside customer feedback trends before drafting a new product proposal or marketing strategy. In this task, you will create a new notebook, add text versions of your Cymbal Foods data files as sources, and interact with NotebookLM to gain insights from this curated collection.

Create a Cymbal Foods analysis notebook
On the home page of your Cymbal Foods Gemini Enterprise app, click NotebookLM on the left-hand navigation pane.

On the NotebookLM home page, in the Link section, click Open, and then click Create New Notebook to start your focused analysis project.

The Add sources dialog will now appear, allowing you to select the documents NotebookLM will use for its analysis. It should look similar to the image below:

NotebookLM Add Sources Dialog

Upload Local Text Files as Sources:
In the "Add sources" dialog, locate and click the Choose file option.
Select the following .txt files (that you downloaded in Task 1)
SALES_data.txt
customer_feedback.txt
MARKETING_data.txt.
Click the Open button to add these files as sources to your NotebookLM notebook.
Wait for NotebookLM to process your selected sources. You should see them listed within your notebook once they are ready.
Your NotebookLM interface should resemble the following:

notebooklm-text-files-uploaded.png

Interacting with Your Sources in NotebookLM
With your Cymbal Foods data loaded into the notebook, you can now ask targeted questions and explore the content.

Take a moment to observe the NotebookLM interface. You'll typically see your added sources listed (often on the left side of the screen), a central area for the chat section, and a Studio section for your notes and the Audio Overview.

Ask Grounded Questions to NotebookLM: In the chat input bar, type your questions. NotebookLM will generate answers based only on the content of the documents you've uploaded into this specific notebook. Try the following prompts:

Query 1:
What audience did the Spring Cookie Promotion target?
Copied!
Query 2:
Summarize the key themes from the customer_feedback.txt regarding products that had sales figures reported in Q1 2025 in the SALES_data.txt.
Copied!
Query 3:
Are there any marketing campaigns in MARKETING_data.txt that correspond to products with significant customer feedback or notable sales trends from the other documents?
Copied!
Query 4 (General inquiry about a single document):
What are the top 3 most frequently mentioned positive aspects in the customer_feedback.txt?
Copied!
Notice how NotebookLM formulates its answers. It should be evident that the information is drawn directly from your uploaded text files. Many grounded AI tools, including NotebookLM, will provide citations or highlight the source text used to generate an answer. Look for these, as they help you verify the information and easily refer back to the original context in your documents. NotebookLM is designed for iterative exploration. You can ask follow-up questions to dive deeper into specific points.

It's different from the broader Gemini Enterprise search (which queries across all connected data) or the specialized Data Analyst Agent (which operates based on its specific programmed instructions). NotebookLM offers a flexible environment for any collection of sources you want to study closely.

A key feature of NotebookLM is often the ability to save AI responses, key passages from sources, or your own thoughts as "notes" within the notebook. This helps you build a structured understanding and gather material for reports or further work. (You can briefly explore if you see options like "Add to notes" or a dedicated notes panel).

Beyond textual summaries and Q&A, NotebookLM offers an innovative way to synthesize the information from your sources through its 'Audio Overview' feature. This allows you to generate a spoken, podcast-like summary of the key insights and themes derived from the documents you've added to your notebook, providing an alternative way to review and understand your material.

In the Studio section, click Audio Overview.
generate audio overview in notebooklm

Listen and Reflect: Take a listen to the Audio Overview once it's generated. Consider how accurately it captured the main themes from your uploaded Cymbal Foods documents. Think about how this audio format could be useful for quickly grasping key points or for reviewing material when reading isn't convenient.
Click Check my progress to verify the objective.
Focused analysis with NotebookLM.

Continue Exploration (Optional):

You've now covered the core exercises for NotebookLM and Gemini Enterprise in this lab! If you have remaining time, this is an excellent opportunity to delve deeper and experiment further with both tools:

In NotebookLM:

Experiment with Different Questions: Go beyond the example queries. Try asking for comparisons, potential implications (based only on the text), or asking NotebookLM to explain a concept using examples from your documents.
Explore Note-Taking: Investigate the "Notes" feature more thoroughly. Try saving key AI responses, extracting important quotes from your sources into notes, and writing your own annotations to build a more structured understanding.
Test Other Features: Look for other available features in the NotebookLM interface, such as suggested questions or different views, and see how they can aid your analysis.
In Your Gemini Enterprise App:

General AI Assistant: Return to the main search/chat interface of your deployed Cymbal Foods - Gemini Enterprise app. Try different or more complex queries. For example, ask it to compare information that might come from different connected data stores (e.g., "Are there any marketing campaigns for products that have recent customer feedback entries in Drive, and do any of these align with upcoming calendar events?").
Review the Gen-Z Campaign Ideas: Revisit the Idea Generation session to explore the marketing concepts your team of AI agents produced.
Analyze the Deep Research Findings: Return to the Deep Research agent to examine the detailed report and its actionable insights.
Explore Pre-built Agents: In the "Agents" section of your Gemini Enterprise app's homepage, see if any Google-provided example agents are listed and try interacting with them to understand their capabilities.
This self-directed exploration is a great way to solidify what you've learned and discover even more potential uses for these powerful AI tools in managing and understanding enterprise information.






