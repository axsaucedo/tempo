
![GitHub](https://img.shields.io/badge/Version-0.1.0-green.svg)
![GitHub](https://img.shields.io/badge/Python-3.5—3.8-blue.svg)
![GitHub](https://img.shields.io/badge/License-Apache-black.svg)

# ⏳ Tempo: The MLOps Software Development Kit

An open source SDK to streamline workflows to productionise machine learning models at scale.

## Overview

Tempo provides a unified interface to multiple MLOps projects that enable data scientists to deploy and productionise machine learning systems.

## Motivating Example

Tempo allows you to interact with scalable orchestration engines like Seldon Core and KFServing, and leverage a broad range of machine learning services like TFserving, Triton, MLFlow, etc.

```python
sklearn_model = Model(
        name="test-iris-sklearn",
        platform=ModelFramework.SKLearn,
        uri="gs://seldon-models/sklearn/iris")

sklearn_model = Model(
        name="test-iris-sklearn",
        platform=ModelFramework.SKLearn,
        uri="gs://seldon-models/sklearn/iris")

@pipeline(name="mypipeline",
          uri="gs://seldon-models/custom",
          models=[sklearn_model, xgboost_model])
class MyPipeline(object):

    @predictmethod
    def predict(self, payload: np.ndarray) -> np.ndarray:
        res1 = sklearn_model(payload)
        if res1[0][0] > 0.7:
            return res1
        else:
            return xgboost_model(payload)

my_pipeline = MyPipeline()

# Deploy only the models into kubernetes
my_pipeline.deploy_models()
my_pipeline.wait_ready()

# Run the request using the local pipeline function but reaching to remote models
my_pipeline.predict(np.array([[4.9, 3.1, 1.5, 0.2]]))
```

## Productionisation Workflows

### Declarative Interface

Even though Tempo provides a dynamic imperative interface, it is possible to convert into a declarative representation of components.

```python
yaml = my_pipeline.to_k8s_yaml()

print(yaml)
```

### Environment Packaging

You can also manage the environments of your pipelines to introduce reproducibility of local and production environments.

```python
@pipeline(name="mypipeline",
          uri="gs://seldon-models/custom",
          conda_env="tempo",
          models=[sklearn_model, xgboost_model])
class MyPipeline(object):
    # ...

my_pipeline = MyPipeline()

# Save the full conda environment of the pipeline
my_pipeline.save()
# Upload the full conda environment
my_pipeline.upload()

# Deploy the full pipeline remotely
my_pipeline.deploy()
my_pipeline.wait_ready()

# Run the request to the remote deployed pipeline
my_pipeline.remote(np.array([[4.9, 3.1, 1.5, 0.2]]))
```

## Examples


