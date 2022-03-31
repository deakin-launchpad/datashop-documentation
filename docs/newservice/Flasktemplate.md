# Deploying an ML Service using Flask and docker for datashop

to begin with we recommend you watch our video tutorial on Part 1

![type:video](../video/Datashop tutorial part-1.mp4)

Software requirements:

* docker
* python 3.+

##### Clone the template
click the following link [service template](https://github.com/manaspalaparthi/service-template.git) and clone the repository to your local machine

    git clone https://github.com/manaspalaparthi/service-template.git

### Template sturcture

    service-template
    ├── Datashop
    │   ├── __init__.py
    │   ├── preprocess.py
    │   ├── postproces.py
    │   └── backend.py
    ├── app.py
    ├── main.py
    ├── service.py
    ├── requirements.txt
    └── model
        ├── __init__.py
        └── model.py


**Out of all the files in the template, only the following files are required to be modified:**

* main.py (update job status)
* service.py (actual service itself)

    
### Prepare your model

1. Create a new directory in the service-template repository called model if not exists
2. copy your model file to the model directory
3. create a __init__.py file in the model directory if not exists
4. Initalize your model in __init__.py file as shown below

example model / __init__.py file :

```angular2html
import tensorflow as tf

# load model
try:
    model = tf.keras.models.load_model("model/EfficientNetB0/")
    print("Model loaded")
except:
    print("Model not found")

```

### Write your Inference script 

To write your inference script, please use service.py file from the root folder of the service-template repository.

all the code you write in service.py file will be executed when the service is called.

user input data such as images/ csv / json are saved in "tmp" folder of the service-template repository.

read the documentation of "service.run" function to know how to read the data from the "tmp" folder.

    title:: 
      run
        description:: 
        Run the model/get the predictions according the service.
    inputs::
      jobID 
            Job ID from datashop application used for search file or save file
    returns::
      insightsDataFileLocation

    load data from temp folder
        >  json data is data.json
        >  all images and CSV are named with jobID_"filetype"
        >  jobiD_csv.csv   "61ef72ed396fc5330c15f250_csv.csv"
        >  jobiD_image.png   "61ef72ed396fc5330c15f250_image.png"

to read the data you can use the following code:
    
    # reading images with file name jobID-image.png or jobID-image.jpg or jobID-image.jpeg
    # 61ef72ed396fc5330c15f250_image.png or 61ef72ed396fc5330c15f250_image.jpg or 61ef72ed396fc5330c15f250_image.jpeg (inspecific)
    fileslist = glob.glob(os.getcwd()+"/tmp/"+jobID+"-image"+"*")

Sample run function:

in this example we are searching for image files in tmp folder with the job-ID and performing inference on the image.
```angular2html

def run(jobID):
  print("************************ \n\n excuting jobID:"+ str(jobID)+" \n\n\n")
  """
  title:: 
      run
  description:: 
      Run the model/get the predictions according the service.
  inputs::
      jobID 
            Job ID from datashop application used for search file or save file

  returns::
      insightsDataFileLocation
      

  load data from temp folder
    >  json data is data.json
    >  all images and CSV are named with jobID_"filetype"
    >  jobiD_csv.csv   "61ef72ed396fc5330c15f250_csv.csv"
    >  jobiD_image.png   "61ef72ed396fc5330c15f250_image.png"
  """

  fileslist = glob.glob(os.getcwd()+"/tmp/"+jobID+"-image"+"*")

  #load image as numpy array
  img = cv2.imread(fileslist[0])
  img = cv2.resize(img, (224, 224))
  img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
  img = np.expand_dims(img, axis=0)
  result  = model.predict(img)

  if result[0][0] > 0.5:
    result = {
      "Autistic": "Positive",
      "confidence":str(result[0][0])
    }

  else:
    result = {
      "Autistic": "Negative",
      "confidence": str(result[0][0])
    }
  print("model inference finished!", str(result))

  return [result]

```

Translate your model results into insights by converting them to Json / image / csv / graphs and return them as a list.  

we support multiple insights as a list. (**Json is default on 0th location of the list**)

### Handle multiple results

    (comming soon)

### Test your model

To test the service, please run 

    python app.py

The service will be up and running on "http://localhost:5000/predict" or "http://127.0.0.1:5000/predict"

we have provided a sample payload for the service in the root folder of the service-template repository. Use [Postman](https://www.getpostman.com/) to test the service as shown in the video.