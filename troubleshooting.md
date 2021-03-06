# MLeap Troubleshooting

## Must provide a sample dataset for the X transformer

This error occurs because you are trying to serialize a Spark
transformer that normally relies on metadata available in the Spark
DataFrame. In order to serialize properly, MLeap needs access to the
metadata so we can store all of the necessary values in the MLeap
Bundle. The solution is to provide a sample DataFrame that has been
transformed by your Spark ML Pipeline.

### Fixed Code

```scala
// Use your Spark ML Pipeline to transform the Spark DataFrame
val transformedDataset = sparkTransformer.transform(sparkDataset)

// Create a custom SparkBundleContext and provide the transformed DataFrame
implicit val sbc = SparkBundleContext().withDataset(transformedDataset)

// Serialize the pipeline as you would normally
(for(bf <- managed(BundleFile(file))) yield {
  sparkTransformer.writeBundle.save(bf).get
}).tried.get
```
