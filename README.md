# StayNest Azure Data Factory Pipeline

The objective of this project is to built a metadata-driven Azure Data Factory (ADF) pipeline for the StayNest hotel booking platform to automate ingestion of CSV files from the Raw layer to the Bronze layer in Azure Data Lake Storage Gen2. This project demonstrates usage of Linked Services, Datasets, Copy Activity, Get Metadata, and ForEach activities.

## Linked Service and Datasets

### Linked Service

`LS_storageaccount` - The linked service is used to establish a connection between Azure Data Factory and the Azure Data Lake Storage Gen2 account.

### Datasets

The following datasets are used in the pipelines:

- `ds_source` – Source dataset that points to a specific CSV file in the `raw` folder.
- `ds_sink` – Sink dataset that points to the `bronze` folder.
- `ds_raw_folder` – Dataset that points to the `raw` folder and is used by the Get Metadata activity.
- `ds_meta_raw` – Parameterized source dataset used to dynamically select files from the `raw` folder.
- `ds_meta_bronze` – Destination dataset that points to the `bronze` folder.

### Activities Used

The pipelines use the following Azure Data Factory activities:

- **Copy Data** – Copies files from the `raw` folder to the `bronze` folder.
- **Get Metadata** – Retrieves metadata from the source folder, specifically the `childItems` used to identify the files available for processing.
- **ForEach** – Iterates over the files returned by the Get Metadata activity and processes each file dynamically.

## Activities in `stay_data_pipeline`

The **Copy Data** activity copies a file from the source dataset, which directly points to a specific CSV file, and loads it into the Bronze folder.

The **Get Metadata** activity takes the source folder as input and retrieves metadata about the files available in that folder. In this pipeline, we use the `childItems` property to identify the files present in the source folder.

The output of the **Get Metadata** activity is shown below. The format is json. It returns a list of child items, where each item contains the **name** and **type** of the file.

```text
{
	"childItems": [
		{
			"name": "bookings.csv",
			"type": "File"
		},
		{
			"name": "customers.csv",
			"type": "File"
		},
		{
			"name": "hotels.csv",
			"type": "File"
		}
	],
	"effectiveIntegrationRuntime": "AutoResolveIntegrationRuntime (Central India)",
	"executionDuration": 0,
	"durationInQueue": {
		"integrationRuntimeQueue": 9
	},
	"billingReference": {
		"activityType": "PipelineActivity",
		"billableDuration": [
			{
				"meterType": "AzureIR",
				"duration": 0.016666666666666666,
				"unit": "Hours"
			}
		]
	}
}
```

## Activities in `staynest_metadata_driven_pipeline`

The pipeline dynamically discovers files available in the `raw` folder and copies them to the `bronze` folder.

The metadata-driven pipeline uses:

1. **Get Metadata** activity to retrieve the files from the Raw folder using `childItems`.
2. **ForEach** activity to iterate through each file.
3. **Copy Data** activity to copy each file to the Bronze folder.
4. The current file name is passed dynamically into source dataset parameter using:

```text
@item().name
```

## Folder Structure

```text
staynest/
├── raw/
│   ├── hotels.csv
│   ├── ...
│
└── bronze/
    ├── hotels.csv
    ├── ...
```

## How to Run

1. Open the Azure Data Factory instance.
2. Go to **Author**.
3. Open pipeline.
4. Click **Debug**.
5. Wait for the pipeline execution to complete.
6. Open the **Monitor** tab to verify the activity execution status.
7. Verify that the files from the `raw` folder have been copied to the `bronze` folder.

## Technologies Used

- Azure Data Factory
- Azure Data Lake Storage Gen2
- Get Metadata Activity
- ForEach Activity
- Copy Data Activity
- Parameterized Datasets
