<!-- Output copied to clipboard! -->

<!-----

You have some errors, warnings, or alerts. If you are using reckless mode, turn it off to see inline alerts.
* ERRORs: 0
* WARNINGs: 0
* ALERTS: 3

Conversion time: 0.88 seconds.


Using this Markdown file:

1. Paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β34
* Sat Feb 04 2023 23:06:03 GMT-0800 (PST)
* Source doc: Essay
* This document has images: check for >>>>>  gd2md-html alert:  inline image link in generated source and store images to your server. NOTE: Images in exported zip file from Google Docs may not appear in  the same order as they do in your doc. Please check the images!

----->


<p style="color: red; font-weight: bold">>>>>>  gd2md-html alert:  ERRORs: 0; WARNINGs: 0; ALERTS: 3.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>>  gd2md-html alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p><a href="#gdcalert1">alert1</a>
<a href="#gdcalert2">alert2</a>
<a href="#gdcalert3">alert3</a>

<p style="color: red; font-weight: bold">>>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>



# VOLATILITY 3 \
Under The Hood

**___**


## By ~mystik



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.jpg). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.jpg "image_tooltip")



# INTRODUCTION

Volatility 3 is a powerful memory analysis tool that is widely used in the digital forensics community to extract critical information from volatile memory dumps. The tool provides a wealth of information about the state of a system at the time of memory acquisition, allowing for in-depth analysis of system processes, network connections, and other critical data. In this blog, we will delve into the "under the hood" workings of Volatility 3, exploring its architecture, data structures, and algorithms. We will also discuss how it is used to extract valuable information from memory dumps and what makes it a versatile and indispensable tool for digital forensics professionals. So, buckle up and get ready to dive deep into the world of Volatility 3! 


## VOLATILITY3 UNDER THE HOOD


## In Volatility 3, the process of memory analysis is broken down into several components: 



* memory layers
* Templates
* Objects & symbol tables.


##  All of these components are stored in a single place, called a Context, which acts as a holding area for all the necessary pieces of information and tools needed for memory analysis


## MEMORY LAYERS

Memory data is stored in a complex way and is not always easily accessible. To make sense of it, Volatility 3 uses a special approach called a directed graph. This graph is made up of two types of elements: 



* DataLayers
* TranslationLayers. 

The DataLayers are the endpoints of the graph and hold the actual data. The TranslationLayers are in the middle of the graph and help translate the memory data into a usable format.

For example, given a raw memory image in a file format called LiME and a page file, Volatility 3 can combine them into a single memory layer. When querying for information from this layer, Volatility 3 uses specific algorithms, such as the Intel memory mapping algorithm, to find the data you need. It then directs you in the right direction, either the LiME layer or the swap layer. If the data is stored in the LiME layer, Volatility 3 uses the LiME file format algorithm to find the information needed.

To check for the layers that volatility 3 supports, use the **_frameworkinfo_** plugin.



<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image2.png "image_tooltip")



## TEMPLATES and OBJECTS

A Template in volatility3 refers to the structure of an object in memory. A template contains information such as the size and members of the structure, the location of each member within the structure, and what certain values within a member could mean.

By applying a Template to a memory layer at a specific offset, an Object can be created. This involves reading data from the memory layer and constructing the object based on the information in the Template. When an object is created,  its members can be examined, and pointers are followed to access the data stored within it. Data from the memory layer is only read once when creating an Object, and the data is stored in the Object for later access.

For example, to view the template in volshell, follow the following steps:

Launch volshell3, and change the context to any PID of a running process using the following  command.

cc(‘PID’)

. Each process has a template known as ‘_EPROCESS’ that defines the data structure of a process. To view the template for the process you just changed the context to, use the following command:

dt(‘_EPROCESS’)

Results will be as shown in the screenshot below.


## 

<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image3.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image3.png "image_tooltip")



## SYMBOLS

Volatility 3 utilizes SymbolTable to access symbol information known by most compiled programs. These symbols define the structure and location of templates within the program and are known as an address or a template. Debugging information in the form of symbol lookup tables is generated during the compilation of the program. The symbols are stored in a SymbolSpace within a Context, which can only hold one SymbolSpace but multiple SymbolTable items. The naming convention used to name these symbols is "module!symbol." The information is read from a JSON file that acts as a bridge between Windows PDB files, Linux DWARF files, other symbol formats, and Volatility 3's internal Python representation of  these Templates or Symbols.


## PLUGINS

Plugins in volatility 3 serve as a way for the user interface to gather data from the user and conduct specific analysis on the Context, which as defined before, holds symbol tables and memory layers. This communication between the user interface and the library is facilitated by the configuration tree, which as we discussed before, contains configurable data for the components within the Context. The plugin displays results in a TreeGrid format, allowing for easy processing by library users without needing to understand the specifics of the data or its format.


## OUTPUT RENDERERS

The user interface chooses the best way to present results from the library's output, which is always in a TreeGrid format. I.e, the Command Line Interface might display the results as a text table or output them to an SQLite database or CSV file. The web interface may choose to display it as JSON, which could then be displayed as a table or stored in a database such as Elastic Search and analyzed using a frontend like Kibana. The renderers only need to have basic knowledge of how to process simple data types such as booleans, strings, integers, bytes, and a few specific ones such as disassembly and absent values.


## CONFIGURATION TREE

The configuration tree acts as the communication channel between the calling program and the Volatility 3 library. Components of the library, such as plugins, TranslationLayers, and Automagics, can utilize the configuration tree to specify their required and options to the calling program. 

When the library is called, the calling program provides the necessary information via the configuration tree. This interaction through the configuration tree ensures the library components have the necessary information to execute their functions correctly, while also allowing the calling program to control and customize the library's behavior.


## AUTOMAGIC

Automagics are pre-execution setup tasks that create a favorable context for a plugin by eliminating repetitive and error-prone steps. They work like magic by taking a raw memory image and automatically providing the plugin with the necessary Intel translation layer and accurate symbol table, without the need for the plugin or calling program to specify all details. They simplify the process of preparing the context, making it easier and less prone to errors.
