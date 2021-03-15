# [WIP] Data model versioning pattern

## Usecase
- When you need to manage data and model versions, which is most of the cases in using machine learning for production.

## Architecture
Machine learning model versioning is difficult. Just releasing a model into production system requires various components whose versioning and naming need to be managed.

- Purpose of using machine learning for the project
  - Value (stock price forecast, anomaly detection, face recognition, etc)
  - Function (image classification, regression, ranking, etc)
- Release version
  - prerelease
  - released
- Input and output data and interface
  - input datatype and structure
  - output datatype and structure
- Code and pipeline
  - Training code and pipeline
  - Inference code and pipeline
  - Environment to run the code and pipeline: library, OS, language, infrastructure and their version
- Hyperparameters
  - Depends on algorithm and library
- Data
  - Data retrieval
  - Splitting of training and testing data (and evaluation data)

In order to make your machine learning reproducible, you need to manage the versions of these components, with reproducibility of the components and model. Since these components are usually managed in different repositories or DWHs, having globally unique names across warehouses is preferable.<br>
This is one suggested versioning scheme:<br>
`model-name x.y.z-data.split` 

- Model name (`MODEL-NAME x.y.z-data.split`)
- Release version (`model-name X.y.z-data.split`)
- Interface version for input and output (`model-name x.Y.z-data.split`)
- Logic version including algorithm, library and hyperparameter (`model-name x.y.Z-data.split`)
- Data retrieval (`model-name x.y.z-DATA.split`)
- Data store version (`model-name x.y.z-data.SPLIT`)

#### Model name (`MODEL-NAME x.y.z-data.split`)
You ought to give a name to your model. In order to avoid losing track of a model in your communication, a name is mandatory. Also, people feel attached to something with a name. Giving a name based on the model's value or function may make it easier to identify, though naming conventions may be defined by your team.<br>

#### Release version (`model-name X.y.z-data.split`)
There are two conditions for a model: prerelease and released. For prereleased model, you give `0` to `X` and for released, `1` or higher.<br>

#### Interface version for input and output (`model-name x.Y.z-data.split`)
A machine learning model gives a prediction to your input data. It may differ depending on your use case and algorithm, but it is will always require an input and return a prediction. When the shape of a model's inputs or outputs change the external interface must be changed accordingly, so managing the interface version to correspond with external system is recommended. Those interfaces, as well as their datatypes and structure, are managed in a code repository, such as Git. It may be good to use Swagger or Protocol Buffers to publicize your API definition externally. <br>
In the interface versioning, you should only manage the version for your public interface, and not the internals of the model, algorithm, or libraries. The algorithm and library versions are managed in the logic portion of the versioning scheme, and model evaluation metrics should be stored in a model repository. For the versioning rule, you may start with `0` and increment to ensure that each change to the interface gets a unique version number.

#### Logic version including algorithm, library and hyperparameter (`model-name x.y.Z-data.split`)
Training an ML algorithm may include many parts, including preprocessing and prediction algorithms (linear regression, logistic regression, SVMs, CNN, etc.), as well as optimization and loss fuctions. Usage varies by language and libraries, and tunable parameters may be available as well. The logic version controls preprocessing, algorithm, library, and parameters that do not change the interface. These changes are usually managed in a code repository as a branch. Experimentation and development tends to involve exploring a diverse array of algorithms, libraries, and parameters, so it's not necessary to track every version (attempting to do so would result in dozens of versions and quickly get out of hand). Start version management once the model is ready for your team to review.<br>
There may be a chance that model's evaluation metrics or loss may differ each time it's trained even without changing the algorithm or its paramaters. In that case, you may store the evaluation metrics along with a training version in your model repository. Since it is difficult to control this variation, it is better to manage as a set of training and evaluation instead of encoding it into the logic version.

#### Data retrieval (`model-name x.y.z-DATA.split`) and Release version (`model-name X.y.z-data.split`)
Data will be stored in some kind of DWH. Since the dataset to be used for training will be defined in the training phase, the training, validation (and possibly test) data should be stored separately. An important point of data versioning is to enable retrieving the same data, without mixing up training and testing, so the trained model can be evaluated in a consistent manner. To enable restoring the dataset exactly, it is best to manage both the data retrieval method and the split method. The resulting split dataset should also be stored in appropriate DWH's (record in a table in database for structured data, and for unstructured data, a zip in an object or file storage system).<br>
It is necessary to manage versions for the data retrieval method (`model-name x.y.z-DATA.split`) and the split dataset (`model-name x.y.z-data.SPLIT`). String versioning or timestamps may be preferable to an incrementing integer. The data retrieval method should be managed as query or data pipeline definition in Github (or other code repository), and the dataset in a DWH.
<br>

---

The interface, logic, and data retrieval are defined in programming code, so they will be recorded in a code repository. If you manage them in branch, then those will naturally be controlled in the code repository's workflow, such as gitflow or github flow.<br>
It is good to manage the model file and its evaluation metrics in a model repository. Various model repositories exist and and they aren't well-standardized, so you may need to adapt your versioning scheme to your model repository's workflow.<br>
The dataset would be stored in your DWH or storage. The table or bucket should be named to correspond to the version.


## Diagram
### Versioning
![diagram0](diagram0.png)

### Version control in each stage
![diagram1](diagram1.png)


## Pros
- Able to manage model versions.
- Manage components to make model creation reproducible.
- Retraining and update management.

## Cons
- Code, model and data are managed in multiple environments.

## Needs consideration
- Selecting repository and DWH.
- How to use them, including workflow.
