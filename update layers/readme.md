## What this Notebook does
This Notebook performs a 1:M spatial join between two datasets. It is intended to support use cases where one of the two layers represents features (of any geometry type) that an organization is interested in monitoring for impacts from another layer (of any geometry type). The output of the join is a feature layer that contains 1 copy of every "impacting" feature for each feature in the "monitoring" layer that is impacted. The purpose of this is to be able to then relate an individual feature that an organization is monitoring to all of the features that are spatially intersecting it. For example, this could be used to identify all of the weather alerts that are impacting a specific office. 

In order to enable this Notebook to both run on a schedule and publish the initial results, the output of this Notebook is identified using a unique combination of an item's title and tag, which are exposed as variables that the user can modify. Before performing the spatial join, the Notebook first queries the active portal for that combination of title and tag, and then:
1. If the result of this query is empty (no matching items), then the Notebook sets the output of the Spatial Join to be a new Item, giving it the name and tag as defined by the user. This could occur because an existing output layer was renamed, its tags were modified, the user running the Notebook does not have permission to access the output, or (ideally) because this is the first time the Notebook is being run. 
1. If the result of this query is exactly one item, then the Notebook will not create set the output of the Spatial Join to be a new Item. Instead, the Notebook will access the result of the Spatial Join as a FeatureSet. All pre-existing features in the matching item will be dropped from the table, and the data from the FeatureSet will then be written to the service.
1. If the result of this query is more than one item, then the Notebook will print a list of matching items and do nothing.

## Variables you'll need to define
| Variable | Description |
| - | - |
| **output_item_title** | This will be the name of the output item created by this Notebook. This name does not have to be unique in your portal, however a unique name and uniqueness tag combination is required. Therefore, a unique name is safer than a name that already is in use. |
| **output_item_uniqueness_tag** | This tag will be added the output item created by this Notebook. This tag does not have to be unique in your portal, however a unique name and uniqueness tag combination is required. Therefore a unique tag is safer than a tag that is already in use |
| **monitor_item_id** | The Item ID that corresponds to the data which you want to monitor. |
| **monitor_layer_id** | 	The Layer ID that corresponds to the layer from the item you want to monitor. |
| **impact_item_id** | 	The Item ID that corresponds to the data which you want to watch for impacts from. |
| **impact_layer_id** | 	The Layer ID that corresponds to the layer from the item you want to watch for impacts from. |

## How you could take this further
There are some features which could enhance the utility of this Notebook, but which are not implemented:
- You may want to set the Notebook up to automatically Email an administrator if the Notebook fails to run successfully - for example if it finds "duplicate" items.
- You may want to set the Notebook up to send email alerts if a particular threshold of impact is detected. Don't spam people though!
- You could use the same approach to creating/updating data with other analytics available through the ArcGIS API for Python to support other use cases.

## Be Aware
This Notebook is intended for demonstration purposes only, and may have unknown issues that would result in incomplete, inaccurate, or defective data. *Because this Notebook automatically manages content, exercise extreme caution when executing this Notebook from an account with administrative privileges*. 
- **There are limitations to the scalability and scheduling frequency of this approach**. In many cases, other solutions like ArcGIS Velocity or GeoEvent Server provide more scalable and appropriate solutions for this or similar use cases.
- **This Notebook could create "zombie" content that "comes back" even after it has been deleted**. If the the output layer is deleted and this Notebook is running on a schedule, then the next time the Notebook executes a new "identical" layer will be created. 
- **This Notebook could create "duplicate" items, which could impact the scheduled execution of the notebook**. If User A has set the Notebook up to run on a schedule, and User B then runs through this notebook manually and accidentally creates an item with the same name and tag, the scheduled execution of this notebook will fail if User A has permission to see User B's new item.
- **This Notebook may fail unexpectedly for unknown reasons**. Because this Notebook was created as a demonstration, it has not been robustly tested and it is possible that it may fail to run.
