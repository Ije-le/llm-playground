# llm-playground

Using the green "Use this template" button on the top right, create a copy of this repository under your GitHub account, giving it the same name. Open that repository in a codespace, then run the following commands in the terminal:

### Setup

```bash
bash setup.sh
```

That will install llm, a Python tool for interacting with different LLMs, llm-groq, which specifically connects to Groq, and llm-gemini, which connects to Google's models. You will need to generate an API key at https://console.groq.com/keys. Copy that key, then do the following command in the terminal:

```bash
llm keys set groq
```

You will paste your copied key in the terminal (it won't appear, and you might get a small popup with the word "paste" in grey. Wait a couple of seconds and you'll be able to click on it.). To make sure it worked, run this command in the terminal:

```bash
llm -m groq-llama-3.3-70b "10 names for a pet aardvark"
```

Now create an API key for Gemini here: https://aistudio.google.com/apikey. Copy that key, then do the following command in the terminal:

```bash
llm keys set gemini
```

You will paste your copied key in the terminal (it won't appear, and you might get a small popup with the word "paste" in grey. Wait a couple of seconds and you'll be able to click on it.). To make sure it worked, run this command in the terminal:

```bash
llm -m gemini-2.5-flash "10 names for a pet turtle"
```

You can see all of the models you have access to using llm with the following command:

```bash
llm models
```

### Text Models

The llm library allows us to chat with LLMs, but also to pass things to those LLMs _and_ to log our conversations in a SQLITE database. Here's one way we can start: asking it to summarize the specific contents of a web page that we'll fetch using a tool called newspaper4k that allows us to reduce the amount of tokens we send to the LLM (they have limits):

```bash
python -m newspaper --url=https://cnsmaryland.org/2025/08/02/in-the-deep-woods-a-little-owl-teaches-big-lessons/ -of=text | llm -m groq/openai/gpt-oss-120b "summarize this story in 3 paragraphs"
```

Read the story: https://cnsmaryland.org/2025/08/02/in-the-deep-woods-a-little-owl-teaches-big-lessons/ and then compare that to the summary. Is the summary a good description? Why or why not?

PUT YOUR ANSWER HERE. I think the summary provided a good description. It contained the important parts of the story in just three paragraphs.

Now it's your turn: find a story that you wrote or are very familiar with that's published online, and try the same process, altering the command and then evaluating the result.

```bash

python -m newspaper --url=https://streetcarsuburbs.news/health-and-haircuts-college-park-resident-spreads-medical-awareness-through-barbershops/ -of=text | llm -m groq/openai/gpt-oss-120b "summarize this story in 3 paragraphs"
```

YOUR EVALUATION HERE. Again, I find this to be an impressive summary. It compressed the salient parts of the story in just three paragraphs and gives a balanced insight.

### Restructuring Information

Text models can do more than summarize; they can restructure information to make it more useful. We'll take a recent document describing sanctions of Maryland attorneys (the sanctionsfy25.txt file in this repository) and send it to Llama 3.3 using the `cat` command, asking the LLM to produce JSON data of some of the contents.

```bash
cat sanctionsfy25.txt | llm -m groq-llama-3.3-70b "produce only an array of JSON objects based on the text with the following keys: name, sanction, date, description. The date should be in the yyyy-mm-dd format. No yapping." 
```

That "no yapping" thing? That's one of the ways you get LLMs to stop narrating their output and just give you what you specify.

Now it's your turn: find a short (3 pages or less) news story that contains unstructured text, save it as a text file and drop it into the list of files on the left side. Then create a prompt like the one above that turns some elements of it into structured data. Put your prompt in the space below, and below that tell me how the LLM did.

```bash
cat myfile.txt | llm -m groq-llama-3.3-70b "produce only an array of JSON objects based on the text with the following keys: name, date of death, status (deceased/living), party affiliation. The date should be in the yyyy-mm-dd format. No yapping."
```

PUT YOUR EVALUATION HERE. I tried this exercise twice. The first prompt I put in the terminal did not have "party affiliation" as a key. When the response came back, I had a JSON file with four names. Originally, the story headline says APC (a Nigerian political party) had lost 3 key members in 13 months, so I was initially surprised to see a list of four names. I searched the story for each name and found that one of the deceased folks was from a different party and was mentioned in the report for context. That made me rewrite my prompt, just for clarity. Also, only the year of death was mentioned for one of the deceased men, and the JSON file I got reflected that. I think this was a pretty decent job.

### Vision Models

One of the options we have with Groq is to use a "vision" model that can describe the information in certain types of images. Let's test that out using an image from this Maryland physician discipline report: https://www.mbp.state.md.us/BPQAPP/orders/D002592202.265.pdf. The specific vision model we're using only accepts jpg, gif and png files, so I've created a PNG from the first page. Run this command in the terminal:

```bash
llm -m gemini-2.5-flash "what is the license number from this image?" -a md_doc.png
```

You should get something like: "The license number in the image is D25922." as a response.

Now it's your turn: find an image _that you would be ok showing to your grandmother_ and make sure it's a PNG, GIF or JPG file. Drag it into the codespace so you can see it in your list of files, then change the command below so that it refers to your image and asks a different question. Edit the README to do that so I can see your work. How did the LLM do?

```bash
llm -m gemini-2.5-flash "YOUR QUESTION" -a YOUR_FILE 
```

PUT YOUR EVALUATION HERE. 
At first, I got the API key from Gemini but the response I got every time I pasted it in the terminal was: 'Error: API key not valid. Please pass a valid API key.' Somehow I had to restart my code space and tried it again. It worked.
I updated the prompt to:
llm -m gemini-2.5-flash "How many trees are in this image?" -a journey.jpg
Here was the response:

Here's a breakdown:
1.  **Right side, immediate roadside row:** There are 7 distinct bare-branched trees clearly standing in a line next to the road. (Visible in main image and crop 5)
2.  **Right side, background forest:** Behind the row of 7 trees, there is a denser forest of both bare trees and some evergreens. While dense, I can identify approximately 25-30 distinct tree forms (trunks and crowns) in this area. (Visible in main image, crops 5 & 6)
3.  **Left side, immediate roadside and midground forest:** On the left side of the road, the forest is quite dense. Along the immediate edge, I can count about 15-20 distinct bare tree trunks. Behind these, the forest deepens. (Visible in main image and crop 4)
4.  **Left side, deeper background forest:** Extending further back and up the slope on the left, I can identify another 30-40 distinct tree shapes in the more distant parts of this very dense section. (Visible in main image and crop 4)
5.  **Far distance (down the road):** In the small gap where the road goes into the distance, there are approximately 7 more distinct bare trees visible against the sky or lighter background. (Visible in main image and crop 4)
Adding these up:
7 (right foreground) + 28 (right background estimate) + 20 (left midground) + 39 (left deep background estimate) + 7 (far distance) = **101 trees**.

Due to the nature of dense forest and partial visibility, this count is an approximation of clearly distinguishable trees.

I count approximately **101** trees in this image.

I find it easy to believe this response because I am familiar with the environment, but it is difficult to verify based on the image I provided, so I tried witih a second image:
llm -m gemini-2.5-flash "How many humans, tables and chairs are in this image?" -a 2021FOI.jpg
The response from LLM said there are 4 humans, 6 tables and seven chairs. That was incorrect. There are 4 humans, 4 chairs and 3 tables.
I decided to break down the question into three, asking for the number of humans first, then chairs then tables. The response this time was accurate and even detailed in one instance: 

There are **3** tables in the image.
Each table is small and covered with a white cloth, placed between the seated individuals.

I think the LLM's performance in this instance is brilliant, but I will avoid convoluted or complex prompts when dealing with images.

### Audio Models

Another option is to use models that transcribe audio. Listen to [this mp3 file](https://dare.wisc.edu/audio/south-carolina-desegregating-edisto-state-park/) from the Dictionary of American Regional English project at the University of Wisconsin. Click the download link and save the .mp3 file to your computer, then drag it to the file to the list of files on the left. Then, using llm, have the `whisper-large-v3-turbo` model from Groq provide a transcription using that file name.

```bash
llm -m whisper-large-v3-turbo -a YOUR_FILE 
```

How did the LLM do compared to the original transcript?

PUT YOUR EVALUATION HERE. I tried to run the prompt on the terminal, but I kept getting this same response: Error: 'Unknown model: whisper-large-v3-turbo'

### How You Could Use AI

Thinking about news archives, write a few examples of how you might use LLMs to help (beyond writing code). Be specific: don't say that you'll use it to accomplish a larger goal. Instead, say how it could perform or improve specific tasks. I encourage you to think big.

PUT YOUR ANSWERS HERE. Beyond writing codes, one way that I can use LLMs in the context of news archives is for summarization, but not in the regular way of having LLMs turning pages of text into fewer paragraphs. 
Since extracting structured information from text is also a means of summarization, I am thinking along these lines. Summarizing text by extracting relevant data kills multiple birds with one stone:
Summarization
Create a database
Manage tokens
More importantly, this form of summarization helps with identifying patterns that may not be immediately apparent and gives users a better chance at categorizing information based on patterns.

Another use of AI for news archives that I would explore is image parsing. Since image pdfs are not usuallly searchable, pairing them with infromation extracted based on the same images make them much more useful as archived materials.


### Finishing Up

When you are finished, add, commit and push your work to GitHub and submit the URL of the repository in ELMS.