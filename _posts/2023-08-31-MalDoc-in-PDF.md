# Polyglot PDFs Embedded with MalDoc/ActiveMime

![Maldoc](/assets/images/favicon/maldoc.jpg)

Recently, a new technique to create **polyglot pdf embedded with maldoc** was reported by **jpcert**. The file can be opened in Microsoft Word even though it has the magic header and structures of a PDF file. If the file embedded is configured with VBA, the VBA will be executed by opening it in Word. Didier Stevens released a new version of **emldump** to help in parsing this kind of PDF file. 

Luckily, I was able to lay my hands on the samples released by **jpcert** and I set off to analyze them. The samples that were analyzed are:

- 01ac13cec301f1b6b60639cce4935b972701a72bd602d9ceeb6eb2827ab89a36
- 5b677d297fb862c2d223973697479ee53a91d03073b14556f421b3d74f136b9d
- ef59d7038cfd565fd65bae12588810d5361df938244ebad33b71882dcf683058

![PDF Identification](/assets/images/favicon/pdf-identification.png)

### Analysis Steps:

1. **Identification:** Use **pdfid** to gain information about the samples.
   
   ![PDFID All Samples](/assets/images/favicon/pdfid-for-all-pdfs.png)
   
3. **Entropy Check:** Inspect the entropy of the structural components with **pdfid**. As shown, an unusually high count of bytes outside the PDF streams may indicate a suspicious file.
   
   ![PDFID Entropy](/assets/images/favicon/pdfid-entropy-of-all-samples.png)
   
4. **Extraction:** Further investigate using the extra flag for **emldump** to view the initial characters of each part. The activemime component will have a header labeled `activeMime`.
   
   ![Head ASCII Identify](/assets/images/favicon/activex-detect-emldump.png)
   
5. **Dumping:** Use **emldump** to extract components. The output file will contain the activemime component.
   
   ![Dump Activemime](/assets/images/favicon/dump-activemime.png)

6. **OLE Investigation:** With the dumped activemime files in hand, use **oledump** to dive deeper. You will find that the Activemime files have a macro stream which can be identified by the **M** beside the stream.
    
   ![OLE Dump VBA Stream](/assets/images/favicon/oledump-vba-stream.png)
   
7. **VBA Extraction:** Extract the VBA streams from the activemime files using **oledump**. As shown in the screenshot below, you can now view any malicious actions that the maldoc aims to execute.
    
   ![5b Macro](/assets/images/favicon/5b_macro.png)

   ![01 Macro](/assets/images/favicon/01_macro.png)
   
* Analysis done ;)
* Further analysis will be warranted depending on the complexity of the uncovered macro code.

### Alternative Analysis:

1. **OLE Parsing:** Use **oleid** to treat the PDF files as if they were OLE files.
   
   ![5b Oleid](/assets/images/favicon/oleid-for-malpolyglot5a.png)
   
   ![01 Oleid](/assets/images/favicon/01ac-oleid.png)
   
2. **VBA Analysis:** With **olevba**, examine the VBA content. This might include resources like PNGs and JPEGs encoded in base64. The activemime component appears as any other embedded resource appears as, base64 encoded data. As we observed earlier, the active mime component was labeled as a jpeg data. We can use this information to narrow down which base64 data to decode. 
   
   ![Activemime Named as JPEG](/assets/images/favicon/activemime-named-as-jpeg.png)

   Here is a screenshot showing how olevba parses the extra bytes as macro content:
   
   ![Parsed_vba_as_macro](/assets/images/favicon/detected-pdf-structure-as-macro.png)

3. In our sample, the activemime component base64 data appears jumbled up having long whitespaces in between several chunks of the base64.

   ![Jumbled_base64](/assets/images/favicon/jumbled-olevba.png)
   
4. **Decoding:** Decode the base64 encoded data using **cyberchef** to retrieve the activemime component.
   
   ![Cyberchef Activemime](/assets/images/favicon/cyerchef-decode-activemime.png)

* *Incomprehensive Yara rule for detection will be available later at **https://github.com/mystickev/Detections-rules-IOCs/tree/main/YARA-rules***

Cheers :)

References:
* https://blogs.jpcert.or.jp/en/2023/08/maldocinpdf.html
* https://blog.didierstevens.com/2023/08/29/quickpost-analysis-of-pdf-activemime-polyglot-maldocs/
* https://blog.didierstevens.com/2023/08/29/update-emldump-py-version-0-0-12/
* https://github.com/decalage2/oletools/wiki/oleid
* https://github.com/decalage2/oletools/wiki/olevba
