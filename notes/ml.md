# Machine Learning in AWS

Some of the services for the Machine Learning facility are:

## Amazon Rekognition (Computer Vision, and detection in images and videos)
For finding objects, texts, scenes, faces, people etc. in images and videos, using ML. People counting, labeling, content moderation, text detection, face detection and analysis, pathing, celebrity recognition, etc.

Eg; Security Systems identifying people entering inside a building, photo apps automatically tagging friends.

### Usecases
Content Moderation

We can use Rekognition for filtering out inappropriate, offensive or unwanted contents, to enfore community guidelines and more. Used in social media, broadcast media, ecommerce, etc. We can flag sensitive contents for manual review in Amazon Augmented AI (A2I).

## Amazon Transcribe (Speech to text, using DL)
Automatically convert speech to text, using a deep learning process called, Automatic Speech Recognition (ASR).

Other features include, removing Personally Identifiable Information (PII) using Redaction, also suports automatic language identification for multi-lingual audio. Real time trasnscription, multiple language support, speaker identification, custom vocabulary, etc.


## Amazon Polly (Text to Speech)
It is used to turn text into lifelike speech using deep learning, allowing us to create application that talk.

We can also customize the pronunciation of words using pronunciation lexicons, works for acronyms, stylized words. Can generate speech from plain text or from documents marked up with Speech Synthesis Markup Language (SSML) with more customizations, like, emphasizing specific words or phrases, using phonetics, including breathing sounds, whispering, etc.

Creating audio books from written contents, adding voice to mobile apps, building voice enabled news readers, etc.



## Amazon Translate (Text translation, 75+ languages)
For natural and accurate language translation, so it helps us to localize the content, like websites, for international users, and to easily translate large volumes of text efficiently.

For translating customer support tickets, creating multilingual chatbots, etc.


## Amazon Lex (Same tech that powers Alexa, for speech to text, i.e; speech recognition)
To convert speech to text, used for Automatic Speech Recognition. Has extra features of NLU to recognise the intent of the text, callers, etc. Can be used to build chatbots, callcenter bots, etc.. Integration with lambda.


## Amazon Connect (Cloud basded contact center solution)
Connect is the cloud based virtual contact center, can be used for receiving  calls, create contact flows, etc.. Can integrate with other CRM systems or AWS. 80% cheaper than traditional contact center solutions.


## Amazon Comprehend (NLP for text analysis, sentiment analysis, entity recognition, language detection, topic modeling, etc.)
For Natural Langugage Processing, fully managed and serverless service. Uses ML to find the language of the text, Extract key phrases, places, peopole, brands, or even events, Understand how positive or negative the text is, Analyze text using tokenization and parts of speech, automatically organize a collection of text files by topic.

For analyzing social media sentiments for products, extyracting key information from legal documents, etc.

### Amazon Comprehend Medical
To detect and return useful information in unstructured clinical text, like, physician's note, discharge summaries, test results, case notes, etc. Uses NLP to detect Protected Health Information (PHI).

Flow: Store your document in the S3, analyze real-time data with kinesis data firehose, or use amazon transcribe to transcribe patient narratives into text that can be analyzed by Amazon Comprehend Medical.

## Amazon Sagemaker (Managed service for building custom ML models)
This is a fully managed service for developers and data scientists to build ML models. We get a jupyter notebook like environment, can train models, deploy them, AutoML, data labeling, etc..

## Amazon Forecast (For timeseries forecast)
For highly accurate forecasting that uses ML and is a fully managed service. Eg; predict the future sales of a raincoat.

Reduces the forecasting time from monts to hours.

For product demand planning, financial planning, resource planning, etc.

## Amazon Kendra (Intelligent search service)
Fully managed document search powered by ML, it can extract answers from within a document, has natural language search capabilities, learns from user interactions, feedbacck to promote preferred results, ability to manuyally fine-tune search results, etc.. We can query using Natural Language.

First we provide the document, then it indexes called as knowledge indexing, then we can use it.

## Amazon Personalize (Personalization and recommendation service)

Fully managed ML service to build apps with real time personalized recommendations, example, personalized product recommendations/re-ranking, customized direct marketing, etc.

Same technology used by amazon.com. Integrated into existing websites, applications, SMS, email marketing systems, etc. Can be implemented in days, not months.

## Amazon Textract (For extracting texts and structured data from docuemnts)
Automatically extracts text, handwriting and data from any scanned documents using AI and ML.
Can extract data from forms and tables, read and prcess any type of document, reading invoices, financial reports, medical recods, insurance claims, tax forms, ID documents, passports, etc..