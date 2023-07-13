# ANALYSIS OF CVE-2023-36884 DOCX FILE

I'll be conducting a brief analysis of a currently active zero-day vulnerability that's being exploited for remote code execution. This vulnerability is classified as CVE-2023-36884, also known as the **"Office and Windows HTML Remote Code Execution Vulnerability."**

From an initial examination, it appears that the exploit is carried out through a specially crafted Office document. Interestingly, the document in question does not contain any VBA, attachments, or external relationships, such as URLs. In other words, at face value, it appears clean.

The crux of the attack strategy lies in the attacker creating the Office document and successfully convincing potential victims to click on it. Once this is achieved, the exploit is executed, potentially allowing the attacker remote access to the victim's system.

For our walkthrough, I will be using a particular file that can be safely obtained from the Malware Bazaar database. The link to download the file is:

https://bazaar.abuse.ch/download/a61b2eafcf39715031357df6b01e85e0d1ea2e8ee1dfec241b114e18f7a1163f/

**Note**

Remember, our objective here is to better understand this zero-day exploit and devise countermeasures to prevent its misuse. Please ensure that you handle the document safely and responsibly. Always execute the file in a controlled environment, preferably an isolated virtual machine, to avoid unintended consequences.

## SAMPLE

This includes a download link for the sample file, followed by the file's MD5 and SHA256 hashes.

|              |                                                                                                                     |
|--------------|---------------------------------------------------------------------------------------------------------------------|
| Sample Link  | [Download Link](https://bazaar.abuse.ch/download/a61b2eafcf39715031357df6b01e85e0d1ea2e8ee1dfec241b114e18f7a1163f 'Malware Bazaar') |
| MD5          | d227874863036b8e73a3894a19bd25a0                                                                                    |
| SHA256       | a61b2eafcf39715031357df6b01e85e0d1ea2e8ee1dfec241b114e18f7a1163f                                                    |


## IOCs

| ID | Type     | Evidence                                                                                     | MD5 Hash                                                                                            |
|----|----------|----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| 1  | Ole File | OLE2LINK                                                                                     | c66977e283ff702652263788a0d20b81                                                                    |
| 2  | Ole File | Msxml2.SAXXMLReader.5.0\x00                                                                  | ed315c3b36a83206dfd1bba013b91575                                                                    |
| 3  | URL      | http://74.50.94.156/MSHTML_C7/start.xml                                                      | https://www.virustotal.com/gui/url/c3067f90f524bb3040ded4809ee+
|    |          |                                                                                              | +5eb043d5f2c0b10ca66bd66f3add72c06e1b3 |
| 4  | URL      | \\104.234.239.26\share1\MSHTML_C7\file001.url                                                | https://www.virustotal.com/gui/ip-address/104.234.239.26                                            |
| 5  | RTF File | afchunk.rtf                                                                                  | 3ca154da4b786a7c89704d0447a03527                                                                    |
                                                                                                                                                                                                                 


## ANALYSIS

The analysis starts by checking if the document is a valid ole file with oleid. As shown in the screenshot below, the file is identified as an openxml(docx) file with no VBA, XML, or External relationships.

![Oleid-Original-file](/assets/images/favicon/oleid-on-original-file.png)

To start analyzing such files, the first step I take is to extract them as if they are zip files. This is because the .docx file format typically consists of XML files that are compressed.

Upon unzipping, I am presented with the usual structure of a docx document.

![DOCX-Structure](/assets/images/favicon/xml-directory-structure.png)

After examining the content for a bit, I discovered a suspicious-looking RTF (Rich Text Format) document within the "word" folder.

![RTF-FILE](/assets/images/favicon/rtf-document.png)

Upon inspection using the rtfobj tool, it becomes clear that the file contains two embedded objects, as shown below.

![RTF-EMBEDDED-OBJETCS](/assets/images/favicon/rtf-objects.png)

These objects can be individually extracted using the following commands:

    rtfobj -s 0 afchunk.rtf
    rtfobj -s 1 afchunk.rtf

In these commands, the '-s' flag is used to specify the stream number of the object you want to extract, while 'afchunk.rtf' is the name of the file being analyzed.

![2nd-emebedded-object](/assets/images/favicon/2nd-object.png)

Alternatively, you can use the tool 'rtfdump' as follows:

    python2 ../rtfdump_V0_0_3/rtfdump.py -f O ../Malicious-analysis/unzipped-doc/word/afchunk.rtf

This command will search for all objects within the RTF file.

The results indicate that there are indeed two objects within the file. This confirms the results I had earlier findings obtained with 'rtfobj'. 

![RTFDUMP](/assets/images/favicon/rtfdump-obj.png)

To extract these files individually, you can use the following commands:

    python2 rtfdump.py -s 152 -H -c "0x21:0xE00l" -d ../Malicious-analysis/unzipped-doc/word/afchunk.rtf > OLE2LINK22
    python2 rtfdump.py -s 161 -H -c "0x21:0x800l" -d ../Malicious-analysis/unzipped-doc/word/afchunk.rtf > OLE2LINK22

![OLE-EMBEDDED-FILES](/assets/images/favicon/OLE-EMBEDDED-FILES.png)

Interestingly, it appears that the second file, OLE2LINK, changes during the extraction with 'rtfdump'. Due to this, I would recommend using 'rtfobj' for more reliable file extraction.

Once extracted, both dumped files turn out to be OLE files with minimal data. 

Upon examining the first file - OLE2LINK, I don't find much except for what looks like an SMB share URL: 

**\\104.234.239.26\share1\MSHTML_C7\file001.url.**

![RAW-FILE](/assets/images/favicon/raw-file-open.png)

Virustotal results on the URL indicate that the share is used to fetch an executable **WINWORD.exe**

### 2nd OLE Obj

On examining the second OLE object in hexadecimal format, I find the following URL: 

**http://74.50.94.156/MSHTML_C7/start.xml.**

![OLE-url](/assets/images/favicon/URL-in-rtf-obj-dll.png)

The launch event of this URL is managed by a [URL moniker function](https://learn.microsoft.com/en-us/windows/win32/com/url-monikers). This is due to the presence of the URL moniker CLSID 79eac9e0-baf9-11ce-8c82-00aa004ba90b (InprocServer32) adjacent to the link, as shown below.

![URL-moniker](/assets/images/favicon/url-moniker.png)

For further assessment, I relied on the results from VirusTotal and Joe's Hybrid Analysis on these files to understand the activities conducted by the URLs.
That's because, during the time of my analysis, the domains mentioned were not accessible. As a result, I wasn't able to continue with this fascinating analysis.

For more information regarding the nature of files that are dropped by these URLs, I found the following graph, courtesy of [zcracga](https://twitter.com/@zcracga '@zcracga'), very helpful!

![FLOWCHART](/assets/images/favicon/flowchart-.jpeg)

Have Fun! :)
