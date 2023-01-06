# Deployment of Roberta MultiClass Classifier for Hate Speech Detection using Django App REST API Framework
This repository is a Django Application for deployment of Transformer Model **Roberta** for classification of Text into hate classes defined as:
* not_hate
* offensive
* implicit_hate
* offensive

The final code used for training and modelling for hate speech classification can be found in the github repository [nlphatespeech](https://github.com/uadR1/nlphatespeech)
Disclaimer! The work in the linked repository is a group work however involves contribution from a team of individuals.

In addition to that, the current repository is only a customized version of github repository [etai_deployment_server](https://github.com/wtah/etai_deployment_server). 

The purpose of this markdown file is to highlight the **key features** of Django application as well as the general **workflow of Django REST API Framework**. On top of that, the customizations made to adapt the framework accoridng to the specific use case of **Hate Speech Detection**. Using the proposed framework trained ML models can be integrated into any application of interest

## Advantages of Django 
* Fast and easy setup with few lines of code (avoiding hassle to write codes from scratch) 
* Scalable design for buidling wesbites or APIs
* Easy to integration in Machine Learning Models due to python environment of Django and Ml Models framework

## Architecture
![architecture](https://github.com/hinatanvir/etai_deployment_server/blob/main/architecture.jpg)

## High Level API Structure 
A typical Django REST API is a combination of view, a url and a serialiser.[reference](https://medium.com/geekculture/an-introduction-to-listcreateapiview-in-django-f37f02a47a3f). A few of important structure elements can be described as:
* **Models** manage the relationship between a database table and python (models.py)
* **Serializers:** validate and serialize incoming and outgoing data (serializer.py)
* **Querysets:** which query construct, query, and store the results of database queries as model instances.
* **Views** which are classes that wrap up a model, serializer, queryset for each endpoint.
* **URLs:** which specify when to call the View.
* **WSGI:** Web Server Gateway to helps the app communicate with the web server (wsgi.py)

In addition to these database configuration details, **INFERENCE** mode, **DEBUG** mode etc. are configured in **settings.py**. Other tasks like interacting with project using CLI. Starting the Webserver or syncing to database are handled by **mangage.py**

## Django App Workflow
User connect to Django web server via browser and submit a request.data(). Inside the Django Web application, a URL mapper will redirect this request to the appropriate view and the view will send appropriate HTTP responses back to the user.

**ListViewAPICreate** function is used to customize the application. This allows GET/POST methods. The details of this function can be summarized by [Reference 1](https://medium.com/geekculture/an-introduction-to-listcreateapiview-in-django-f37f02a47a3f)following steps:
* Accept request.data()
* Preprocess the data validated by the serializer (serializer.is_valid())
* The data is further tokenised using the **RobertaTokenizerFast** return by **get_text_model** function. 
> encoded_input = self.tokenizer(self.preprocess(text), `return_tensors='tf'`) 
* Prediction is made on the tokenised data via tensorflow model trained using **TFRobertaForSequenceClassification**. The ML part of this github is can be found in another github  [here](https://github.com/uadR1/nlphatespeech)
* serializer.save() will save in a database readable format is **DO_SAVE_PREDICTIONS=TRUE** in settings.py
* The response.data() is saved by Return the prediction and confidence score as a JSON( JavaScript Object Notation).
> ***get_serializer() here is the serializer_class = **TextPredictionSerializer*****. 

The prediction scores against each label class are actually the relative confidence scores of the model against each class and these are obtained by processing the logits received from the transformer models  before the final activation layer. The scores are zipped together with the label class in dictionary format for clear readability and understanding of the user. The code can be referred to in **infer** function under **views.py**

# References 
For implementation on your PCs follow the step explained at github repository [etai_deployment_server](https://github.com/wtah/etai_deployment_server). Below links can be useful to further understand Django application and frameworks 
* [Reference 1](https://medium.com/geekculture/an-introduction-to-listcreateapiview-in-django-f37f02a47a3f)
* [Reference 2](https://medium.datadriveninvestor.com/deploying-ml-models-using-django-rest-api-part-2-84cea50b3c83)
* [Reference 3](https://github.com/nature1995/AI-Image-classifiers-on-Django-with-RESTAPI). In case the deployment is extended to cloud and integrated with Jenknins or similar platform for continuously intergred automated MLOPs Pipeline.
