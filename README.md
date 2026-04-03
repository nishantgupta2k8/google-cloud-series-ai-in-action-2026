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




lab-04-build-multi-agent-systems-with-adk

GENAI106
Overview
This lab covers orchestrating multi-agent systems within the Google Agent Development Kit (Google ADK).

This lab assumes that you are familiar with the basics of ADK and tool use as covered in the labs:

Get started with Google Agent Development Kit (ADK)
Empower ADK agents with tools
Objectives
In this lab, you will:

Create multiple agents and relate them to one another with parent to sub-agent relationships.
Build content across multiple turns of conversation and multiple agents by writing to a session's state dictionary.
Instruct agents to read values from the session state to use as context for their responses.
Use workflow agents to pass the conversation between agents directly.
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
Multi-Agent Systems
The Agent Development Kit empowers developers to get more reliable, sophisticated, multi-step behaviors from generative models. Instead of writing long, complex prompts that may not deliver results reliably, you can construct a flow of multiple, simple agents that can collaborate on complex problems by dividing tasks and responsibilities.

This architectural approach offers several key advantages such as:

Easier to design: You can think in terms of agents with specific jobs and skills.
Specialized functions with more reliable performance: Specialized agents can learn from clear examples to become more reliable at their specific tasks.
Organization: Dividing the workflow into distinct agents allows for a more organized, and therefor easier to think about, approach.
Improvability and maintainability: It is easier to improve or fix a specialized component rather than make changes to a complex agent that may fix one behavior but might impact others.
Modularity: Distinct agents from one workflow can be easily copied and included in other similar workflows.
The Hierarchical Agent Tree
Tree structure showing hierarchical agents

In Agent Development Kit, you organize your agents in a tree-like structure. This helps limit the options for transfers for each agent in the tree, making it easier to control and predict the possible routes the conversation can take through the tree. Benefits of the hierarchical structure include:

It draws inspiration from real-world collaborative teams, making it easier to design and reason about the behavior of the multi-agent system.
It is intuitive for developers, as it mirrors common software development patterns.
It provides greater control over the flow of information and task delegation within the system, making it easier to understand possible pathways and debug the system. For example, if a system has two report-generation agents at different parts of its flow with similar descriptions, the tree structure makes it easier to ensure that the correct one is invoked.
The structure always begins with the agent defined in the root_agent variable (although it may have a different user-facing name to identify itself). The root_agent may act as a parent to one or more sub-agents. Each sub-agent agent may have its own sub-agents.

Task 1. Install ADK and set up your environment
In this lab environment, the Vertex AI API has been enabled for you. If you were to follow these steps in your own project, you would enable it by navigating to Vertex AI and following the prompt to enable it.

Prepare a Cloud Shell Editor tab
With your Google Cloud console window selected, open Cloud Shell by pressing the G key and then the S key on your keyboard. Alternatively, you can click the Activate Cloud Shell button (Activate Cloud Shell) in the upper right of the Cloud console.
Click Continue.
When prompted to authorize Cloud Shell, click Authorize.
In the upper right corner of the Cloud Shell Terminal panel, click the Open in new window button Open in new window button.
Click the Open Editor pencil icon (Edit pencil icon) at the top of the pane to view files.
At the top of the left-hand navigation menu, click the Explorer icon (Explorer icon) to open your file explorer.
Click the Open Folder button.
In the Open Folder dialog that opens, click OK to select your Qwiklab student account's home folder.
Close any additional tutorial or Gemini panels that appear on the right side of the screen to save more of your window for your code editor.
Throughout the rest of this lab, you can work in this window as your IDE with the Cloud Shell Editor and Cloud Shell Terminal.
Download and install ADK and code samples for this lab
Paste the following commands into the Cloud Shell Terminal to copy code files from a Cloud Storage bucket for this lab:

gcloud storage cp -r gs://YOUR_GCP_PROJECT_ID-bucket/* .
Copied!
Update your PATH environment variable, install ADK, and install additional lab requirements by running the following commands in the Cloud Shell Terminal.

export PATH=$PATH:"/home/${USER}/.local/bin"
python3 -m pip install google-adk -r adk_multiagent_systems/requirements.txt
Copied!
Task 2. Explore transfers between parent, sub-agent, and peer agents
The conversation always begins with the agent defined as the root_agent variable.

The default behavior of a parent agent is to understand the description of each sub-agent and determine if control of the conversation should be transferred to a sub-agent at any point.

You can help guide those transfers in the parent's instruction by referring to the sub-agents by name (the values of their name parameter, not their variable names). Try an example:

In the Cloud Shell Terminal, run the following to create a .env file to authenticate the agent in the parent_and_subagents directory.

cd ~/adk_multiagent_systems
cat << EOF > parent_and_subagents/.env
GOOGLE_GENAI_USE_VERTEXAI=TRUE
GOOGLE_CLOUD_PROJECT=YOUR_GCP_PROJECT_ID
GOOGLE_CLOUD_LOCATION=global
MODEL=gemini_flash_model_id
EOF
Copied!
Run the following command to copy that .env file to the workflow_agents directory, which you will use later in the lab:

cp parent_and_subagents/.env workflow_agents/.env
Copied!
In the Cloud Shell Editor file explorer pane, navigate to the adk_multiagent_systems/parent_and_subagents directory.

Click on the agent.py file to open it.

Tip: Because Python code requires that we define our sub-agents before we can add them to an agent, in order to read an agent.py file in the order of the conversation flow, you may want to start reading with the bottom agent and work back towards the top.
Notice that there are three agents here:

a root_agent named steering (its name is used to identify it in ADK's dev UI and command line interfaces). It asks the user a question (if they know where they'd like to travel or if they need some help deciding), and the user's response to that question will help this steering agent know which of its two sub-agents to steer the conversation towards. Notice that it only has a simple instruction that does not mention the sub-agents, but it is aware of its sub-agents' descriptions.
a travel_brainstormer that helps the user brainstorm destinations if they don't know where they would like to visit.
an attractions_planner that helps the user build a list of things to do once they know which country they would like to visit.
Make travel_brainstormer and attractions_planner sub-agents of the root_agent by adding the following line to the creation of the root_agent:

sub_agents=[travel_brainstormer, attractions_planner]
Copied!
Save the file.

Note that you don't add a corresponding parent parameter to the sub-agents. The hierarchical tree is defined only by specifying sub_agents when creating parent agents.

In the Cloud Shell Terminal, run the following to use the ADK command line interface to chat with your agent:

cd ~/adk_multiagent_systems
adk run parent_and_subagents
Copied!
When you are presented the [user]: prompt, greet the agent with:

hello
Copied!
Example output (yours may be a little different):

user: hello
[steering]: Hi there! Do you already have a country in mind for your trip, or would you like some help deciding where to go?
Tell the agent:

I could use some help deciding.
Copied!
Example output (yours may be a little different):

user: I could use some help deciding.
[travel_brainstormer]: Okay! To give you the best recommendations, I need to understand what you're looking for in a trip.
...
Notice from the name [travel_brainstormer] in brackets in the response that the root_agent (named [steering]) has transferred the conversation to the appropriate sub-agent based on that sub-agent's description alone.

At the user: prompt, enter exit to end the conversation.

You can also provide your agent more detailed instructions about when to transfer to a sub-agent as part of its instructions. In the agent.py file, add the following lines to the root_agent's instruction:

If they need help deciding, send them to
'travel_brainstormer'.
If they know what country they'd like to visit,
send them to the 'attractions_planner'.
Copied!
Save the file.

In the Cloud Shell Terminal, run the following to start the command line interface again:

adk run parent_and_subagents
Copied!
Greet the agent with:

hello
Copied!
Reply to the agent's greeting with:

I would like to go to Japan.
Copied!
Example output (yours may be a little different):

user: I would like to go to Japan.
[attractions_planner]: Okay, I can help you with that! Here are some popular attractions in Japan:

*   **Tokyo:**
    *   Senso-ji Temple
    *   Shibuya Crossing
    *   Tokyo Skytree
*   **Kyoto:**
    ...
Notice that you have been transferred to the other sub-agent, attractions_planner.

Reply with:

Actually I don't know what country to visit.
Copied!
Example output (yours may be a little different):

user: actually I don't know what country to visit
[travel_brainstormer]: Okay! I can help you brainstorm some countries for travel...
Notice you have been transferred to the travel_brainstormer agent, which is a peer agent to the attractions_planner. This is allowed by default. If you wanted to prevent transfers to peers, you could have set the disallow_transfer_to_peers parameter to True on the attractions_planner agent.

At the user prompt, type exit to end the session.

Step-by-step pattern: If you are interested in an agent that guides a user through a process step-by-step, one useful pattern can be to make the first step the root_agent with the second step agent its only sub-agent, and continue with each additional step being the only sub-agent of the previous step's agent.
Click Check my progress to verify the objective.
Explore transfers between parent, sub-agent, and peer agents

Task 3. Use session state to store and retrieve specific information
Each conversation in ADK is contained within a Session that all agents involved in the conversation can access. A session includes the conversation history, which agents read as part of the context used to generate a response. The session also includes a session state dictionary that you can use to take greater control over the most important pieces of information you would like to highlight and how they are accessed.

This can be particularly helpful to pass information between agents or to maintain a simple data structure, like a list of tasks, over the course of a conversation with a user.

To explore adding to and reading from state:

Return to the file adk_multiagent_systems/parent_and_subagents/agent.py

Paste the following function definition after the # Tools header:

def save_attractions_to_state(
    tool_context: ToolContext,
    attractions: List[str]
) -> dict[str, str]:
    """Saves the list of attractions to state["attractions"].

    Args:
        attractions [str]: a list of strings to add to the list of attractions

    Returns:
        None
    """
    # Load existing attractions from state. If none exist, start an empty list
    existing_attractions = tool_context.state.get("attractions", [])

    # Update the 'attractions' key with a combo of old and new lists.
    # When the tool is run, ADK will create an event and make
    # corresponding updates in the session's state.
    tool_context.state["attractions"] = existing_attractions + attractions

    # A best practice for tools is to return a status message in a return dict
    return {"status": "success"}
Copied!
In this code, notice:

The session is passed to your tool function as ToolContext. All you need to do is assign a parameter to receive it, as you see here with the parameter named tool_context. You can then use tool_context to access session information like conversation history (through tool_context.events) and the session state dictionary (through tool_context.state). When the tool_context.state dictionary is modified by your tool function, those changes will be reflected in the session's state after the tool finishes its execution.
The docstring provides a clear description and sections for argument and return values.
The commented function code demonstrates how easy it is to make updates to the state dictionary.
Add the tool to the attractions_planner agent by adding the tools parameter when the agent is created:

tools=[save_attractions_to_state]
Copied!
Add the following bullet points to the attractions_planner agent's existing instruction:

- When they reply, use your tool to save their selected attraction
and then provide more possible attractions.
- If they ask to view the list, provide a bulleted list of
{ attractions? } and then suggest some more.
Copied!
Notice the section in curly braces: { attractions? }. This ADK feature, key templating, loads the value of the attractions key from the state dictionary. The question mark after the attractions key prevents this from erroring if the field is not yet present.

You will now run the agent from the web interface, which provides a tab for you to see the changes being made to the session state. Launch the Agent Development Kit Web UI with the following command:

adk web --allow_origins "regex:https://.*\.cloudshell\.dev"
Copied!
Output

INFO:     Started server process [2434]
INFO:     Waiting for application startup.
+-------------------------------------------------------+
| ADK Web Server started                                |
|                                                       |
| For local testing, access at http://localhost:8000.   |
+-------------------------------------------------------+

INFO:     Application startup complete.
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit) 
To view the web interface in a new tab, click the http://127.0.0.1:8000 link in the Terminal output.

A new browser tab will open with the ADK Dev UI.

From the Select an agent dropdown on the left, select the parent_and_subagents agent from the dropdown.

Start the conversation with: hello

After the agent greets you, reply with:

I'd like to go to Egypt.
Copied!
You should be transferred to the attractions_planner and be provided a list of attractions.

Choose an attraction, for example:

I'll go to the Sphinx
Copied!
You should receive an acknowledgement in the response, like: Okay, I've saved The Sphinx to your list. Here are some other attractions...

Click the response tool box (marked with a check mark) to view the event created from the tool's response. Notice that it includes an actions field which includes state_delta describing the changes to the state.

You should be prompted by the agent to select more attractions. Reply to the agent by naming one of the options it has presented.

On the left-hand navigation menu, click the "X" to exit the focus on the event you inspected earlier.

Now in the sidebar, you should see the list of events and a few tab options. Select the State tab. Here you can view the current state, including your attractions array with the two values you have requested.

Session State preview in the Web UI

Send this message to the agent:

What is on my list?
Copied!
It should return your list formatted as a bulleted list according to its instruction.

When you are finished experimenting with the agent, close the web browser tab and press CTRL + C in the Cloud Shell Terminal to stop the server.

Later in this lab, you will demonstrate how to use state to communicate between agents.

Click Check my progress to verify the objective.
Use session state to store and retrieve specific information

Instead of saving small pieces of information, if you would like to store your agent's entire text response in the state dictionary, you can set an output_key parameter when you define the agent, and its entire output will be stored in the state dictionary under that field name.
Workflow Agents
Parent to sub-agent transfers are ideal when you have multiple specialist sub-agents, and you want the user to interact with each of them.

However, if you would like agents to act one-after-another without waiting for a turn from the user, you can use workflow agents. Some example scenarios when you might use workflow agents include when you would like your agents to:

Plan and Execute: When you want to have one agent prepare a list of items, and then have other agents use that list to perform follow-up tasks, for example writing sections of a document
Research and Write: When you want to have one agent call functions to collect contextual information from Google Search or other data sources, then another agent use that information to produce some output.
Draft and Revise: When you want to have one agent prepare a draft of a document, and then have other agents check the work and iterate on it
To accomplish these kinds of tasks, workflow agents have sub-agents and guarantee that each of their sub-agents acts. Agent Development Kit provides three built-in workflow agents and the opportunity to define your own:

SequentialAgent
LoopAgent
ParallelAgent
Throughout the rest of this lab, you will build a multi-agent system that uses multiple LLM agents, workflow agents, and tools to help control the flow of the agent.

Specifically, you will build an agent that will develop a pitch document for a new hit movie: a biographical film based on the life of a historical character. Your sub-agents will handle the research, an iterative writing loop with a screenwriter and a critic, and finally some additional sub-agents will help brainstorm casting ideas and use historical box office data to make some predictions about box office results.

In the end, your multi-agent system will look like this (you can click on the image to see it larger):

Diagram of a film_concept_team multi-agent system

But you will begin with a simpler version.

Task 4. Begin building a multi-agent system with a SequentialAgent
The SequentialAgent executes its sub-agents in a linear sequence. Each sub-agent in its sub_agents list is run, one after the other, in the order they are defined.

This is ideal for workflows where tasks must be performed in a specific order, and the output of one task serves as the input for the next.

In this task, you will run a SequentialAgent to build a first version of your movie pitch-development multi-agent system. The first draft of your agent will be structured like this:

Film_concept_team multi-agent system step 1

A root_agent named greeter to welcome the user and request a historical character as a movie subject

A SequentialAgent called film_concept_team will include:

A researcher to learn more about the requested historical figure from Wikipedia, using a LangChain tool covered in the lab Empower ADK agents with tools. An agent can choose to call its tool(s) multiple times in succession, so the researcher can take multiple turns in a row if it determines it needs to do more research.
A screenwriter to turn the research into a plot outline.
A file_writer to title the resulting movie and write the results of the sequence to a file.
In the Cloud Shell Editor, navigate to the directory adk_multiagent_systems/workflow_agents.

Click on the agent.py file in the workflow_agents directory.

Read through this agent definition file. Because sub-agents must be defined before they can be assigned to a parent, to read the file in the order of the conversational flow, you can read the agents from the bottom of the file to the top.

You also have a function tool append_to_state. This function allows agents with the tool the ability to add content to a dictionary value in state. It is particularly useful for agents that might call a tool multiple times or act in multiple passes of a LoopAgent, so that each time they act their output is stored.

Try out the current version of the agent by launching the web interface from the Cloud Shell Terminal. You will use the --reload_agents argument to enable live reloading of agents based on agent changes:

cd ~/adk_multiagent_systems
adk web --allow_origins "regex:https://.*\.cloudshell\.dev" --reload_agents
Copied!
Note: If you did not shut down your previous adk web session, the default port of 8000 will be blocked, but you can launch the Dev UI with a new port by using adk web --port 8001, for example.
To view the web interface in a new tab, click the http://127.0.0.1:8000 link in the Terminal output.

A new browser tab will open with the ADK Dev UI.

From the Select an agent dropdown on the left, select workflow_agents.

Start the conversation with: hello. It may take a few moments for the agent to respond, but it should request you enter a historical figure to start your film plot generation.

When prompted to enter a historical figure, you can enter one of your choice or use one of these examples:

Zhang Zhongjing - a renowned Chinese physician from the 2nd Century CE.
Ada Lovelace - an English mathematician and writer known for her work on early computers
Marcus Aurelius - a Roman emperor known for his philosophical writings.
The agent should now call its agents one after the other as it executes the workflow and writes the plot outline file to your ~/adk_multiagent_systems/movie_pitches directory. It should inform you when it has written the file to disk.

If you don't see the agent reporting that it generated a file for you or want to try another character, you can click + New Session in the upper right and try again.

View the agent's output in the Cloud Shell Editor. (You may need to use the Cloud Shell Editor's menu to enable View > Word Wrap to see the full text without lots of horizontal scrolling.)

In the ADK Dev UI, click on one of the agent icons (agent_icon) representing a turn of conversation to bring up the event view.

The event view provides a visual representation of the tree of agents and tools used in this session. You may need to scroll in the event panel to see the full plot.

adk web graph

In addition to the graph view, you can click on the Request tab of the event to see the information this agent received as part of its request, including the conversation history.
You can also click on the Response tab of the event to see what the agent returned.
Note: While this system can produce interesting results, it is not intended to imply that instructions can be so brief or adding examples can be skipped. The system's reliability would benefit greatly from the additional layer of adding more rigorous instructions and examples for each agent.
Click Check my progress to verify the objective.
Begin building a multi-agent system with a SequentialAgent

Task 5. Add a LoopAgent for iterative work
The LoopAgent executes its sub-agents in a defined sequence and then starts at the beginning of the sequence again without breaking for a user input. It repeats the loop until a number of iterations has been reached or a call to exit the loop has been made by one of its sub-agents (usually by calling a built-in exit_loop tool).

This is beneficial for tasks that require continuous refinement, monitoring, or cyclical workflows. Examples include:

Iterative Refinement: Continuously improve a document or plan through repeated agent cycles.
Continuous Monitoring: Periodically check data sources or conditions using a sequence of agents.
Debate or Negotiation: Simulate iterative discussions between agents to reach a better outcome.
You will add a LoopAgent to your movie pitch agent to allow multiple rounds of research and iteration while crafting the story. In addition to refining the script, this allows a user to start with a less specific input: instead of suggesting a specific historical figure, they might only know they want a story about an ancient doctor, and a research-and-writing iteration loop will allow the agents to find a good candidate, then work on the story.

Film_concept_team multi-agent system step 2

Your revised agent will flow like this:

The root_agent greeter will remain the same.
The film_concept_team SequentialAgent will now consist of:
A writers_room LoopAgent that will begin the sequence. It will consist of:
The researcher will be the same as before.
The screenwriter will be similar to before.
A critic that will offer critical feedback on the current draft to motivate the next round of research and improvement through the loop.
When the loop terminates, it will escalate control of the conversation back to the film_concept_team SequentialAgent, which will then pass control to the next agent in its sequence: the file_writer that will remain as before to give the movie a title and write the results of the sequence to a file.
To make these changes:

In the adk_multiagent_systems/workflow_agents/agent.py file, add this tool import so that you can provide an agent the ability to exit the loop when desired:

from google.adk.tools import exit_loop
from google.adk.models import Gemini
Copied!
To determine when to exit the loop, add this critic agent to decide when the plot outline is ready. Paste the following new agent into the agent.py file under the # Agents section header (without overwriting the existing agents). Note that it has the exit_loop tool as one of its tools and instructions on when to use it:

critic = Agent(
    name="critic",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Reviews the outline so that it can be improved.",
    instruction="""
    INSTRUCTIONS:
    Consider these questions about the PLOT_OUTLINE:
    - Does it meet a satisfying three-act cinematic structure?
    - Do the characters' struggles seem engaging?
    - Does it feel grounded in a real time period in history?
    - Does it sufficiently incorporate historical details from the RESEARCH?

    If the PLOT_OUTLINE does a good job with these questions, exit the writing loop with your 'exit_loop' tool.
    If significant improvements can be made, use the 'append_to_state' tool to add your feedback to the field 'CRITICAL_FEEDBACK'.
    Explain your decision and briefly summarize the feedback you have provided.

    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    RESEARCH:
    { research? }
    """,
    before_model_callback=log_query_to_model,
    after_model_callback=log_model_response,
    tools=[append_to_state, exit_loop]
)
Copied!
Create a new LoopAgent called writers_room that creates the iterative loop of the researcher, screenwriter, and critic. Each pass through the loop will end with a critical review of the work so far, which will prompt improvements for the next round. Paste the following above the existing film_concept_team SequentialAgent.

writers_room = LoopAgent(
    name="writers_room",
    description="Iterates through research and writing to improve a movie plot outline.",
    sub_agents=[
        researcher,
        screenwriter,
        critic
    ],
    max_iterations=5,
)
Copied!
Note that the LoopAgent creation includes a parameter for max_iterations. This defines how many times the loop will run before it ends. Even if you plan to interrupt the loop via another method, it is a good idea to include a cap on the total number of iterations.

Update the film_concept_team SequentialAgent to replace the researcher and screenwriter with the writers_room LoopAgent you just created. The file_writer agent should remain at the end of the sequence. The film_concept_team should now look like this:

film_concept_team = SequentialAgent(
    name="film_concept_team",
    description="Write a film plot outline and save it as a text file.",
    sub_agents=[
        writers_room,
        file_writer
    ],
)
Copied!
Return to the ADK Dev UI tab and click the + New Session button in the upper right to start a new session.

Begin a new conversation with: hello

When prompted to choose a kind of historical character, choose one that interests you. Some ideas include:

an industrial designer who made products for the masses
a cartographer (a map maker)
that guy who made crops yield more food
Once you have chosen a type of character, the agent should work its way through iterations of the loop and finally give the film a title and write the outline to a file.

Using the Cloud Shell Editor, review the file generated, which should be saved in the adk_multiagent_systems/movie_pitches directory. (Once again, you may need to use the Editor's menu to enable View > Word Wrap to see the full text without lots of horizontal scrolling.)

Click Check my progress to verify the objective.
Add a LoopAgent for iterative work

Task 6. Use a "fan out and gather" pattern for report generation with a ParallelAgent
The ParallelAgent enables concurrent execution of its sub-agents. Each sub-agent operates in its own branch, and by default, they do not share conversation history or state directly with each other during parallel execution.

This is valuable for tasks that can be divided into independent sub-tasks that can be processed simultaneously. Using a ParallelAgent can significantly reduce the overall execution time for such tasks.

In this lab, you will add some supplemental reports -- some research on potential box office performance and some initial ideas on casting -- to enhance the pitch for your new film.

Film_concept_team multi-agent system step 3

Your revised agent will flow like this:

The greeter will the same.
The film_concept_team SequentialAgent will now consist of:
The writers_room LoopAgent, which will remain the same including:
The researcher agent
The screenwriter agent
The critic agent
Your new preproduction_team ParallelAgent will then act, consisting of:
A box_office_researcher agent to use historical box office data to generate a report on potential box office performance for this film
A casting_agent agent to generate some initial ideas on casting based on actors who have starred in similar films
The file_writer that will remain as before to write the results of the sequence to a file.
While much of this example demonstrates creative work that would be done by human teams, this workflow represents how a complex chain of tasks can be broken across several sub-agents to produce drafts of complex documents which human team members can then edit and improve upon.

Paste the following new agents and ParallelAgent into your workflow_agents/agent.py file under the # Agents header:

box_office_researcher = Agent(
    name="box_office_researcher",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Considers the box office potential of this film",
    instruction="""
    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    INSTRUCTIONS:
    Write a report on the box office potential of a movie like that described in PLOT_OUTLINE based on the reported box office performance of other recent films.
    """,
    output_key="box_office_report"
)

casting_agent = Agent(
    name="casting_agent",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Generates casting ideas for this film",
    instruction="""
    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    INSTRUCTIONS:
    Generate ideas for casting for the characters described in PLOT_OUTLINE
    by suggesting actors who have received positive feedback from critics and/or
    fans when they have played similar roles.
    """,
    output_key="casting_report"
)

preproduction_team = ParallelAgent(
    name="preproduction_team",
    sub_agents=[
        box_office_researcher,
        casting_agent
    ]
)
Copied!
Update the existing film_concept_team agent's sub_agents list to include the preproduction_team between the writers_room and file_writer:

film_concept_team = SequentialAgent(
    name="film_concept_team",
    description="Write a film plot outline and save it as a text file.",
    sub_agents=[
        writers_room,
        preproduction_team,
        file_writer
    ],
)
Copied!
Update the file_writer's instruction to:

INSTRUCTIONS:
- Create a marketable, contemporary movie title suggestion for the movie described in the PLOT_OUTLINE. If a title has been suggested in PLOT_OUTLINE, you can use it, or replace it with a better one.
- Use your 'write_file' tool to create a new txt file with the following arguments:
    - for a filename, use the movie title
    - Write to the 'movie_pitches' directory.
    - For the 'content' to write, include:
        - The PLOT_OUTLINE
        - The BOX_OFFICE_REPORT
        - The CASTING_REPORT

PLOT_OUTLINE:
{ PLOT_OUTLINE? }

BOX_OFFICE_REPORT:
{ box_office_report? }

CASTING_REPORT:
{ casting_report? }
Copied!
Save the file.

In the ADK Dev UI, click + New Session in the upper right.

Enter hello to start the conversation.

When prompted, enter a new character idea that you are interested in. Some ideas include:

that actress who invented the technology for wifi
an exciting chef
key players in the worlds fair exhibitions
When the agent has completed its writing and report-generation, inspect the file it produced in the adk_multiagent_systems/movie_pitches directory. If a part of the process fails, click + New session in the upper right and try again.

Custom workflow agents
When the pre-defined workflow agents of SequentialAgent, LoopAgent, and ParallelAgent are insufficient for your needs, CustomAgent provides the flexibility to implement new workflow logic. You can define patterns for flow control, conditional execution, or state management between sub-agents. This is useful for complex workflows, stateful orchestrations, or integrating custom business logic into the framework's orchestration layer.

Creation of a CustomAgent is out of the scope of this lab, but it is good to know that it exists if you need it!



###agent.py (parent_and_subagents)
import os
import sys
import logging

sys.path.append("..")
from callback_logging import log_query_to_model, log_model_response
from dotenv import load_dotenv
import google.cloud.logging
from google.adk import Agent
from google.adk.models import Gemini
from google.genai import types
from typing import Optional, List, Dict

from google.adk.tools.tool_context import ToolContext

sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from adk_utils.plugins import Graceful429Plugin
from google.adk.apps.app import App

load_dotenv()

cloud_logging_client = google.cloud.logging.Client()
cloud_logging_client.setup_logging()

RETRY_OPTIONS = types.HttpRetryOptions(initial_delay=1, max_delay=3, attempts=30)

# Tools (add the tool here when instructed)


# Agents

attractions_planner = Agent(
    name="attractions_planner",
    model=Gemini(model=os.getenv("MODEL"), retry_options=RETRY_OPTIONS),
    description="Build a list of attractions to visit in a country.",
    instruction="""
        - Provide the user options for attractions to visit within their selected country.
        """,
    before_model_callback=log_query_to_model,
    after_model_callback=log_model_response,
    # When instructed to do so, paste the tools parameter below this line

    )

travel_brainstormer = Agent(
    name="travel_brainstormer",
    model=Gemini(model=os.getenv("MODEL"), retry_options=RETRY_OPTIONS),
    description="Help a user decide what country to visit.",
    instruction="""
        Provide a few suggestions of popular countries for travelers.
        
        Help a user identify their primary goals of travel:
        adventure, leisure, learning, shopping, or viewing art

        Identify countries that would make great destinations
        based on their priorities.
        """,
    before_model_callback=log_query_to_model,
    after_model_callback=log_model_response,
)

root_agent = Agent(
    name="steering",
    model=Gemini(model=os.getenv("MODEL"), retry_options=RETRY_OPTIONS),
    description="Start a user on a travel adventure.",
    instruction="""
        Ask the user if they know where they'd like to travel
        or if they need some help deciding.
        """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    # Add the sub_agents parameter when instructed below this line

)

graceful_plugin = Graceful429Plugin(
    name="graceful_429_plugin",
    fallback_text={
        "default": "**[Simulated Response via 429 Graceful Fallback]**\n\nThe API is out of quota. Please retry."
    }
)
graceful_plugin.apply_429_interceptor(root_agent)

app = App(
    name="parent_and_subagents",
    root_agent=root_agent,
    plugins=[graceful_plugin]






###agent.py (workflow_agents)
import os
import logging
import google.cloud.logging
import sys
from google.adk.tools import exit_loop
from google.adk.models import Gemini

sys.path.append("..")
from callback_logging import log_query_to_model, log_model_response
from dotenv import load_dotenv

from google.adk import Agent
from google.adk.agents import SequentialAgent, LoopAgent, ParallelAgent
from google.adk.tools.tool_context import ToolContext
from google.adk.tools.langchain_tool import LangchainTool  # import
from google.adk.models import Gemini
from google.genai import types

sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from adk_utils.plugins import Graceful429Plugin
from google.adk.apps.app import App

from langchain_community.tools import WikipediaQueryRun
from langchain_community.utilities import WikipediaAPIWrapper


cloud_logging_client = google.cloud.logging.Client()
cloud_logging_client.setup_logging()

load_dotenv()

model_name = os.getenv("MODEL")
print(model_name)

RETRY_OPTIONS = types.HttpRetryOptions(initial_delay=1, max_delay=3, attempts=30)

# Tools


def append_to_state(
    tool_context: ToolContext, field: str, response: str
) -> dict[str, str]:
    """Append new output to an existing state key.

    Args:
        field (str): a field name to append to
        response (str): a string to append to the field

    Returns:
        dict[str, str]: {"status": "success"}
    """
    existing_state = tool_context.state.get(field, [])
    tool_context.state[field] = existing_state + [response]
    logging.info(f"[Added to {field}] {response}")
    return {"status": "success"}


def write_file(
    tool_context: ToolContext,
    directory: str,
    filename: str,
    content: str
) -> dict[str, str]:
    target_path = os.path.join(directory, filename)
    os.makedirs(os.path.dirname(target_path), exist_ok=True)
    with open(target_path, "w") as f:
        f.write(content)
    return {"status": "success"}


# Agents


critic = Agent(
    name="critic",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Reviews the outline so that it can be improved.",
    instruction="""
    INSTRUCTIONS:
    Consider these questions about the PLOT_OUTLINE:
    - Does it meet a satisfying three-act cinematic structure?
    - Do the characters' struggles seem engaging?
    - Does it feel grounded in a real time period in history?
    - Does it sufficiently incorporate historical details from the RESEARCH?

    If the PLOT_OUTLINE does a good job with these questions, exit the writing loop with your 'exit_loop' tool.
    If significant improvements can be made, use the 'append_to_state' tool to add your feedback to the field 'CRITICAL_FEEDBACK'.
    Explain your decision and briefly summarize the feedback you have provided.

    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    RESEARCH:
    { research? }
    """,
    before_model_callback=log_query_to_model,
    after_model_callback=log_model_response,
    tools=[append_to_state, exit_loop]
)

file_writer = Agent(
    name="file_writer",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Creates marketing details and saves a pitch document.",
    instruction="""
    INSTRUCTIONS:
    - Create a marketable, contemporary movie title suggestion for the movie described in the PLOT_OUTLINE. If a title has been suggested in PLOT_OUTLINE, you can use it, or replace it with a better one.
    - Use your 'write_file' tool to create a new txt file with the following arguments:
        - for a filename, use the movie title
        - Write to the 'movie_pitches' directory.
        - For the 'content' to write, extract the following from the PLOT_OUTLINE:
            - A logline
            - Synopsis or plot outline

    PLOT_OUTLINE:
    { PLOT_OUTLINE? }
    """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    tools=[write_file],
)

screenwriter = Agent(
    name="screenwriter",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="As a screenwriter, write a logline and plot outline for a biopic about a historical character.",
    instruction="""
    INSTRUCTIONS:
    Your goal is to write a logline and three-act plot outline for an inspiring movie about the historical character(s) described by the PROMPT: { PROMPT? }
    
    - If there is CRITICAL_FEEDBACK, use those thoughts to improve upon the outline.
    - If there is RESEARCH provided, feel free to use details from it, but you are not required to use it all.
    - If there is a PLOT_OUTLINE, improve upon it.
    - Use the 'append_to_state' tool to write your logline and three-act plot outline to the field 'PLOT_OUTLINE'.
    - Summarize what you focused on in this pass.

    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    RESEARCH:
    { research? }

    CRITICAL_FEEDBACK:
    { CRITICAL_FEEDBACK? }
    """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    tools=[append_to_state],
)

researcher = Agent(
    name="researcher",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Answer research questions using Wikipedia.",
    instruction="""
    PROMPT:
    { PROMPT? }
    
    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    CRITICAL_FEEDBACK:
    { CRITICAL_FEEDBACK? }

    INSTRUCTIONS:
    - If there is a CRITICAL_FEEDBACK, use your wikipedia tool to do research to solve those suggestions
    - If there is a PLOT_OUTLINE, use your wikipedia tool to do research to add more historical detail
    - If these are empty, use your Wikipedia tool to gather facts about the person in the PROMPT
    - Use the 'append_to_state' tool to add your research to the field 'research'.
    - Summarize what you have learned.
    Now, use your Wikipedia tool to do research.
    """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    tools=[
        LangchainTool(tool=WikipediaQueryRun(api_wrapper=WikipediaAPIWrapper())),
        append_to_state,
    ],
)
writers_room = LoopAgent(
    name="writers_room",
    description="Iterates through research and writing to improve a movie plot outline.",
    sub_agents=[
        researcher,
        screenwriter,
        critic
    ],
    max_iterations=5,
)

film_concept_team = SequentialAgent(
    name="film_concept_team",
    description="Write a film plot outline and save it as a text file.",
    sub_agents=[
        writers_room,
        file_writer
    ],
)

root_agent = Agent(
    name="greeter",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Guides the user in crafting a movie plot.",
    instruction="""
    - Let the user know you will help them write a pitch for a hit movie. Ask them for   
      a historical figure to create a movie about.
    - When they respond, use the 'append_to_state' tool to store the user's response
      in the 'PROMPT' state key and transfer to the 'film_concept_team' agent
    """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    tools=[append_to_state],
    sub_agents=[film_concept_team],
)

graceful_plugin = Graceful429Plugin(
    name="graceful_429_plugin",
    fallback_text={
        "default": "**[Simulated Response via 429 Graceful Fallback]**\n\nThe API is out of quota. Please retry."
    }
)
graceful_plugin.apply_429_interceptor(root_agent)

app = App(
    name="workflow_agents",
    root_agent=root_agent,
    plugins=[graceful_plugin]
)




###agent.py (previous + ParallelAgent)

import os
import logging
import google.cloud.logging
import sys
from google.adk.tools import exit_loop
from google.adk.models import Gemini

sys.path.append("..")
from callback_logging import log_query_to_model, log_model_response
from dotenv import load_dotenv

from google.adk import Agent
from google.adk.agents import SequentialAgent, LoopAgent, ParallelAgent
from google.adk.tools.tool_context import ToolContext
from google.adk.tools.langchain_tool import LangchainTool  # import
from google.adk.models import Gemini
from google.genai import types

sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from adk_utils.plugins import Graceful429Plugin
from google.adk.apps.app import App

from langchain_community.tools import WikipediaQueryRun
from langchain_community.utilities import WikipediaAPIWrapper


cloud_logging_client = google.cloud.logging.Client()
cloud_logging_client.setup_logging()

load_dotenv()

model_name = os.getenv("MODEL")
print(model_name)

RETRY_OPTIONS = types.HttpRetryOptions(initial_delay=1, max_delay=3, attempts=30)

# Tools


def append_to_state(
    tool_context: ToolContext, field: str, response: str
) -> dict[str, str]:
    """Append new output to an existing state key.

    Args:
        field (str): a field name to append to
        response (str): a string to append to the field

    Returns:
        dict[str, str]: {"status": "success"}
    """
    existing_state = tool_context.state.get(field, [])
    tool_context.state[field] = existing_state + [response]
    logging.info(f"[Added to {field}] {response}")
    return {"status": "success"}


def write_file(
    tool_context: ToolContext,
    directory: str,
    filename: str,
    content: str
) -> dict[str, str]:
    target_path = os.path.join(directory, filename)
    os.makedirs(os.path.dirname(target_path), exist_ok=True)
    with open(target_path, "w") as f:
        f.write(content)
    return {"status": "success"}


# Agents

box_office_researcher = Agent(
    name="box_office_researcher",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Considers the box office potential of this film",
    instruction="""
    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    INSTRUCTIONS:
    Write a report on the box office potential of a movie like that described in PLOT_OUTLINE based on the reported box office performance of other recent films.
    """,
    output_key="box_office_report"
)

casting_agent = Agent(
    name="casting_agent",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Generates casting ideas for this film",
    instruction="""
    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    INSTRUCTIONS:
    Generate ideas for casting for the characters described in PLOT_OUTLINE
    by suggesting actors who have received positive feedback from critics and/or
    fans when they have played similar roles.
    """,
    output_key="casting_report"
)

preproduction_team = ParallelAgent(
    name="preproduction_team",
    sub_agents=[
        box_office_researcher,
        casting_agent
    ]
)

critic = Agent(
    name="critic",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Reviews the outline so that it can be improved.",
    instruction="""
    INSTRUCTIONS:
    Consider these questions about the PLOT_OUTLINE:
    - Does it meet a satisfying three-act cinematic structure?
    - Do the characters' struggles seem engaging?
    - Does it feel grounded in a real time period in history?
    - Does it sufficiently incorporate historical details from the RESEARCH?

    If the PLOT_OUTLINE does a good job with these questions, exit the writing loop with your 'exit_loop' tool.
    If significant improvements can be made, use the 'append_to_state' tool to add your feedback to the field 'CRITICAL_FEEDBACK'.
    Explain your decision and briefly summarize the feedback you have provided.

    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    RESEARCH:
    { research? }
    """,
    before_model_callback=log_query_to_model,
    after_model_callback=log_model_response,
    tools=[append_to_state, exit_loop]
)

file_writer = Agent(
    name="file_writer",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Creates marketing details and saves a pitch document.",
    instruction="""
    INSTRUCTIONS:
    - Create a marketable, contemporary movie title suggestion for the movie described in the PLOT_OUTLINE. If a title has been suggested in PLOT_OUTLINE, you can use it, or replace it with a better one.
    - Use your 'write_file' tool to create a new txt file with the following arguments:
    - for a filename, use the movie title
    - Write to the 'movie_pitches' directory.
    - For the 'content' to write, include:
        - The PLOT_OUTLINE
        - The BOX_OFFICE_REPORT
        - The CASTING_REPORT

    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    BOX_OFFICE_REPORT:
    { box_office_report? }

    CASTING_REPORT:
    { casting_report? }
    """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    tools=[write_file],
)

screenwriter = Agent(
    name="screenwriter",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="As a screenwriter, write a logline and plot outline for a biopic about a historical character.",
    instruction="""
    INSTRUCTIONS:
    Your goal is to write a logline and three-act plot outline for an inspiring movie about the historical character(s) described by the PROMPT: { PROMPT? }
    
    - If there is CRITICAL_FEEDBACK, use those thoughts to improve upon the outline.
    - If there is RESEARCH provided, feel free to use details from it, but you are not required to use it all.
    - If there is a PLOT_OUTLINE, improve upon it.
    - Use the 'append_to_state' tool to write your logline and three-act plot outline to the field 'PLOT_OUTLINE'.
    - Summarize what you focused on in this pass.

    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    RESEARCH:
    { research? }

    CRITICAL_FEEDBACK:
    { CRITICAL_FEEDBACK? }
    """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    tools=[append_to_state],
)

researcher = Agent(
    name="researcher",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Answer research questions using Wikipedia.",
    instruction="""
    PROMPT:
    { PROMPT? }
    
    PLOT_OUTLINE:
    { PLOT_OUTLINE? }

    CRITICAL_FEEDBACK:
    { CRITICAL_FEEDBACK? }

    INSTRUCTIONS:
    - If there is a CRITICAL_FEEDBACK, use your wikipedia tool to do research to solve those suggestions
    - If there is a PLOT_OUTLINE, use your wikipedia tool to do research to add more historical detail
    - If these are empty, use your Wikipedia tool to gather facts about the person in the PROMPT
    - Use the 'append_to_state' tool to add your research to the field 'research'.
    - Summarize what you have learned.
    Now, use your Wikipedia tool to do research.
    """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    tools=[
        LangchainTool(tool=WikipediaQueryRun(api_wrapper=WikipediaAPIWrapper())),
        append_to_state,
    ],
)
writers_room = LoopAgent(
    name="writers_room",
    description="Iterates through research and writing to improve a movie plot outline.",
    sub_agents=[
        researcher,
        screenwriter,
        critic
    ],
    max_iterations=5,
)

film_concept_team = SequentialAgent(
    name="film_concept_team",
    description="Write a film plot outline and save it as a text file.",
    sub_agents=[
        writers_room,
        preproduction_team,
        file_writer
    ],
)

root_agent = Agent(
    name="greeter",
    model=Gemini(model=model_name, retry_options=RETRY_OPTIONS),
    description="Guides the user in crafting a movie plot.",
    instruction="""
    - Let the user know you will help them write a pitch for a hit movie. Ask them for   
      a historical figure to create a movie about.
    - When they respond, use the 'append_to_state' tool to store the user's response
      in the 'PROMPT' state key and transfer to the 'film_concept_team' agent
    """,
    generate_content_config=types.GenerateContentConfig(
        temperature=0,
    ),
    tools=[append_to_state],
    sub_agents=[film_concept_team],
)

graceful_plugin = Graceful429Plugin(
    name="graceful_429_plugin",
    fallback_text={
        "default": "**[Simulated Response via 429 Graceful Fallback]**\n\nThe API is out of quota. Please retry."
    }
)
graceful_plugin.apply_429_interceptor(root_agent)

app = App(
    name="workflow_agents",
    root_agent=root_agent,
    plugins=[graceful_plugin]
)


