**Multi-language Kernels**

**Background**

The Jupyter ecosystem exists under the requirement that a notebook is supported by a single kernel runtime: meaning that users can only execute code in a single language. This decision emerged out of a need to simplify the communication protocols employed by Jupyter, improve the performance of Jupyter notebooks, and simplify the user experience for interacting with notebooks.

Multi-language kernels have been implemented in several applications such as SoS Notebooks and Beaker. 

**Motivations**

Users in data science contexts often use multiple programming languages to manipulate data, train models, and present  their findings. Implementing multi-language kernels would improve the experience for building data science workflows in notebooks.

Different languages offer different toolkits. For example, R is often used to perform complex statistical analysis, whereas Python comes with various frameworks to build and analyze machine learning models. Scientists often switch among these languages to access the libraries they need, and multikernel support will reduce the friction in such environment.

**Goals**

The goals of this session are to implement a specification and action plan for implementing multi-language kernels.

Features:

- Cell-level language support: support executing different languages in interactive and non-interactive mode
- Server-side support for multi-language: implementation not specific to a frontend
- True multiplexing support in the backend
- Support for routing IOPub/Stdin channels
- Front-end agnostic specifications
- Debugging/diagnostic mechanism for multiple kernels (logging, health status etc…)
- [Desired] Accessing variables cross languages
- [Desired] Lazy start for kernels
- [Desired] Protocol for variable listing/viewing
- [Desired] Kernel discovery mechanism
- [Desired] Mechanism for syntax highlighting (probably be frontend dependent, but still worth calling out)

**Existing Approaches**

BeakerX and Zeppelin uses cell-magic to support multi-language. The support is limited to just executing code and rendering output (though Zeppelin output can be richer and more interactive than BeakerX’s model). As we are focusing on Jupyter here, SoS (<https://vatlab.github.io/sos-docs/>) is an implementation with the most extensive features; the following section focuses on SoS kernel exclusively.

#### SoS Kernel

SoS Notebook is a Jupyter extension that supports multilanguage language notebook using multiple kernels.

 

SoS notebook supports both interactive mode and workflow mode (headless execution). Here we focus on how SoS notebook achieves multi-language support.

 

Code cell structure:

 
```
{

  "cell_type": "code",

  "execution_count": 5,

  "metadata": {

​	"kernel": "Bash"

  },

  "outputs": [

​	{

  	"name": "stdout",

​      "output_type": "stream",

  	"text": [

​    	" 	352 1_Expand_and_Capture.ipynb\n"

  	]

​	}

  ],

  "source": [

​	"%capture --to res\n",

​	"%expand\n",

​	"wc -l {filename}"

  ]

}
```
 

Metadata field in a cell is used to store the kernel that was used to execute the code. In interactive mode, SoS relies on front-end interaction to switch language. Specifically, the frontend Javascript sends a “language-switch” message to the backend in two cases:

- User changes the language of a cell
- User clicks on a cell of a different language than the “current one”

*(Users can also switch language using cell magic)*

This model enables SoS to supports not only code executions but also interactions (i.e. Ipywidgets) that relies on the “comm” or “shell” channel.

 

Notable features of SoS kernel:

- Multi-language support
- Data exchange among kernels
- Support code in markdown cells
- Implicit data exchange for variables prefixed with “sos_”
- Metadata of the kernel stored in the cells enables SoS to support headless execution

 

Limitations:

-   Requires Javascript-integration on the frontend
- Technically, still single-kernel in the backend (lacking mechanism to actually multiplex messages based on languages)
- Relying on front-end: multiple browsing sessions on the same notebook will result in unexpected behavior
- Front-end specific implementation: not available in JupyterLab/nteract.

**Known Challenges**

- Jupyter messaging protocol doesn’t support target kernel
- Interactions on the IOPub and Stdin channels (i.e. mapping of messages to languages)

**References**

- SoS: <https://vatlab.github.io/sos-docs/>
- BeakerX polyglot: [[Link](https://github.com/twosigma/beakerx/blob/915685eb831e48b801eced75edb47ff86ca4e983/doc/groovy/PolyglotMagic.ipynb)]
