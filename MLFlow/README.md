## 1. Cài đặt
- ```pip install mlflow```

## 2. Tạo experiment
- Tạo experiment 
```mlflow.create_experiment(name = "testing_mlflow", 
			artifact_location="testing_mlflow", 
			tags={"env":"dev","version":"1.0.0"},
			)
```
- Sau khi chạy nó sẽ tạo thư mục mlruns trong đó chứa id của experiment. Trong experiment sẽ chứa thông tin của experiment đó.
- ```mlflow ui``` : Lệnh này sẽ tạo ra 1 UI để quản lý các version
## 3. Lấy experiment
- Lấy qua id : ```experiment = mlflow.get_experiment(experiment_id)```
- Lấy qua name : ```experiment = mlflow.get_experiment_by_name(experiment_name)```

## 4. Xóa experiment 
- ```mlflow.delete_experiment(experiment_id="$experiment$")```
## 5. MLflow run:
- Cách 1:
```
mlflow.start_run()
mlflow.log_param("learning_rate", 0.001)
mlflow.end_run()
```
- Cách 2:
```
with mlfow.start_run(run_name="mlflow_run") as run:
	mlflow.log_param("learning_rate",0.001)
	print(run.info.run_id)
	print(run.info)
```
- Cách 3:
```
experiment_id = create_mlflow_experiment(
					experiment_name= "testing_mlflow",
					artifact_location="testing_mlflow_artiface",
					tags={"env":"dev","version":"1.0.0"}
					)
experiment = get_mlflow_experiment(experiemt_id=experiemt_id)
with mlfow.start_run(run_name="testing", experiment_id=experiment.experiment_id) as run:
	mlflow.log_param("learning_rate", 0.001)
```
## 6. Log param
- Code mẫu
```
experiment = get_mlflow_experiment(experiment_name="testing_mlfow")
with mlflow.start_run(run_name="log_param", experiment_id= experiment.experiment_id) as run:
	parameters = {
		"learning_rate":0.001
		"epochs": 10,
		"batch_size": 100,
		"loss_function":"mse",
		"optimizer":"SGD"
	}
	mlflow.log_params(parameters)
	
```

- Log metrics:

```
experiment = get_mlflow_experiment(experiment_name="testing_mlfow")
mlfow.log_metric("accuracy": 0.001)
with mlflow.start_run(run_name="log_param", experiment_id= experiment.experiment_id) as run:
	metrics = {
		"mse":0.001
		"rmse": 0.001,
		"recall": 0.001,
		"precission":0.001,
		"f1-score":0.001
	}
	mlflow.log_metrics(metrics)
	
```


- Log artifacts

```
experiment = get_mlflow_experiment(experiment_name="testing_mlfow")
with mlflow.start_run(run_name="log_param", experiment_id= experiment.experiment_id) as run:
	with open("hello_world.txt", 'w') as file:
		file.write("Hello World!")
	
	mlflow.log_artifact(local_path="hello_world.txt", artifact_path= 'hello_world.txt')
	
```
- Log images
```
experiment = get_mlflow_experiment(experiment_name="testing_mlfow")
with mlflow.start_run(run_name="log_param", experiment_id= experiment.experiment_id) as run:
	X,y = make_classification(n_samples=1000, n_features=10, n_infomative=5, random_state=42)
	X_train, X_test, y_train, y_test = train_test_split(X,y, test_size = 0.2, random_state=43)
	rfc = RandomForestClassifier(n_estimators=100, random_state=42)
	rfc.fit(X_train,y_train)
	y_pred = rfc.predict(X_test)
	
	fig_pr = plt.figure()
	pr_display = PrecisionRecallDisplay.from_predictions(y_test, y_pred, ax=plt.gca())
	plt.title("Precision Recall Curve")
	plt.legend()
	mlflow.log_figure(fig_pr,'precision_recall.png')
	
	fig_roc = plt.figure()
	roc_display = RocCurveDisplay.from_predictions(y_test, y_pred, ax=plt.gca())
	plt.title("ROC Curve")
	plt.legend()
	mlflow.log_figure(fig_roc,'roc.png')
	
	fig_cm = plt.figure()
	cm_display = ConfusionMatrixDisplay.from_predictions(y_test, y_pred, ax=plt.gca())
	plt.title("Confusion Curve")
	plt.legend()
	mlflow.log_figure(fig_cm,'confusionmatrix.png')
	
```

- Log model 1
```
experiment = get_mlflow_experiment(experiment_name="testing_mlfow")
with mlflow.start_run(run_name="log_param", experiment_id= experiment.experiment_id) as run:
	X,y = make_classification(n_samples=1000, n_features=10, n_infomative=5, random_state=42)
	X_train, X_test, y_train, y_test = train_test_split(X,y, test_size = 0.2, random_state=43)
	
	mlflow.autolog()
	rfc = RandomForestClassifier(n_estimators=100, random_state=42)
	rfc.fit(X_train, y_train)
	y_pred = rfc.predict(X_test)
```

- Log model 2
```
experiment = get_mlflow_experiment(experiment_name="testing_mlfow")
with mlflow.start_run(run_name="log_param", experiment_id= experiment.experiment_id) as run:
	X,y = make_classification(n_samples=1000, n_features=10, n_infomative=5, random_state=42)
	X_train, X_test, y_train, y_test = train_test_split(X,y, test_size = 0.2, random_state=43)
	
	mlflow.sklearn.autolog()
	
	rfc = RandomForestClassifier(n_estimators=100, random_state=42)
	rfc.fit(X_train, y_train)
	y_pred = rfc.predict(X_test)
	mlflow.sklearn.log_model(sk_model=rfc, artifact_path="random_forest_classifier")
```

## 7. Model inference

```
X,y = make_classification(n_samples=1000, n_features=10, n_infomative=5, random_state=42)
X = pd.DataFrame(X, columns=["feature_{}".fomat(i) for i in range(10)])
y = pd.DataFrame(y, columns=["target"])
_,X_test, _, y_test = train_test_split(X,y, test_size=0.2, random_state=43)
model_uri = f'runs:/{run_id}/random_forest_classifier'
rfc = mlflow.sklearn.load_model(model_uri = model_uri)
y_pred = rfc.predict(X_test)
y_pred = pd.DataFrame(y_pred, columns=["prediction"])
print(y_pred.head())
```

## 8. Model Signature

```
X_train, y_train = get_train_data()
cols_spec = []
data_map = {
	'int64':'interger',
	'float64':'double',
	'bool':'boolean',
	'str':'string',
	'date':'datetime'
	}
	
for name, dtype in x_train.dtypes.to_dict().items():
	cols_spec.append(ColSpec(name=name, type= data_map[str(dtype)]))
input_schema = Schema(inputs=col_spec)
output_schema= Schema([ColSpec(name='label', type="interger")])
parameter = ParamSpec(name="model_name", dtype="string", default="model1")
param_schema = ParamSchema(params=[parameter])

model_signature = ModelSignature(inputs=input_schema, outputs = output_schema, params= param_schema)

model_signature = infer_signature(X_train, y_train)
experiment_id = create_mlflow_experiment(
	experiment_name="model signature"
	artifact_location = "model_signature_artifact"
	tags = {"purpose":"learning"}
)

with mlflow.start_run(run_name='model_signature_run') as run:
	mlflow.sklearn.log_model(
		sk_model= RandomForestClassifier()
		artifact_path='model_signature',
		signature=model_signature
		)
```

## 9. Model Registry

```
class CustomerModel(mlflow.pyfunc.PythonModel):
	def predict(self, context, model_input):
		return model_input
experiment_id = create_mlflow_experiment(
	experiment_name="model_registry"
	artifact_location = "model_registry_artifact"
	tags = {"purpose":"learning"}
)
with mlflow.start_run(run_name="model_registry") as run:
	model = CustomerModel()
	mlflow.pyfunc.log_model(artifact_path="custom_model", python_model= model, registered_model_name="CustomerModel")
	mlflow.pyfunc.log_model(artifact_path="rfr_model1", sk_model= RandomForestRegressor(), registered_model_name="RandomForestRegressor")
	mlflow.pyfunc.log_model(artifact_path="rfr_model2", sk_model= RandomForestRegressor())
```

## Model Registry Operations

experiment_id = create_mlflow_experiment(
	experiment_name="model_registry"
	artifact_location = "model_registry_artifact"
	tags = {"purpose":"learning"}
)
client = MlflowClient()
model_name= "registered_model_1"
source = "$path"
run_id = "$id"
# create model version
client.create_model_version(name= model_name, source=source, run_id=run_id)
# transition stage
client.transition_model_version_stage(name=model_name, version=2, stage="Staging)
#delete model version
client.delete_model_version(name=model_name, version=1)
#delete registered model
client.delete_registered_model(name=model_name)
# add description 
client.update_registered_model(name=model_name, description="This is test model")
client.set_registered_model_tag(name=model_name, key="tag1", value="value1")
client.update_model_version(name=model_name, version=1, description="This is test model version")
# add tags
client.set_model_version_tag(name=model_name, version=1, key="tag1", value="value1")







