# Multi-langauge Kernels Solution Proposal

### Requirements

- Execute each cell using a different language kernel.
- Support for correctly converting multi-language cells with nbconvert when the `--execute` flag is used.
- Add a language field in the Jupyter Messaging Protocol request to label the route messages to the correct kernel.
- The protocol should be front-end agnostic.
- Should only support executing one cell at a time.
- Bundling kernel status and tracking it within the metakernel.
- "Restart Kernel" restarts all the kernels.
- The metakernel should be able to relay messages to a set of user-defined runtimes.
  - It is up to the front-end to decide how to present this configuration UI.
- Need a cell_id in the notebook format to track the relationship between the cell and the runtime it uses within the metakernel.
- [Desired] Provide protocol for exhanging variables across languages.
- [Desired] Support syntax highlighting on a per-cell level.

### Changes to the Notebook Format

1. Add `runtime_info` to top-level metadata that stores the language, name, and langauge_info associated with the runtimes that were configured by the user for that notebook.
2. Notebooks backed by the meta-kernel won't include the full language_info.
3. Add `runtime` and `langauge` to metadata in each cell.

```
{
	"metadata": {
		name: "meta_kernel",
		runtime_info: [
			{
				"name": "Python3-Conda",
				"language_info": {...},
			},
			{
				"name": "Scala-Local",
				"language_info": {...}
			}
		]
	},
	"cells": [
		{
			"cellId" : "ABCXYZ",
			"metadata" : {
				"runtime": "Python3-Conda",
				"language": "python3"
			}
		},
		{
			"cellId" : "ABCXYZ2",
			"metadata" : {
				"runtime": "Scala-Local"
				"language": "scala"
			}
		}
	]
}
```
### Next Steps

1. Create API for notebook and metakernel.
2. Make nteract add language to metadata of kernel messages.
3. Implement the meta kernel.
4. Implement simple client to support meta kernel including jupyter-client, nbconvert, nteract.
5. Add cell_id to nbformat.
