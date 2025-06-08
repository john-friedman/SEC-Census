# SEC Census

A project to standardize and flatten SEC submission metadata, as well as collect essential statistics.

This project is part of [Layer 0](https://datamule.xyz/papers/datamule.pdf).

## Data Wrangling

The data was downloaded using `datamule-python`, which uses `secsgml` to parse SEC SGML files. The process took about 48 hours.

### SGML Variations

Tag Delimited
```
<SUBMISSION>
<ACCESSION-NUMBER>0000950172-95-000354
<TYPE>SC 14D1/A
<PUBLIC-DOCUMENT-COUNT>2
<FILING-DATE>19950915
<SROS>NYSE
<SUBJECT-COMPANY>
<COMPANY-DATA>
<CONFORMED-NAME>SOUTHERN PACIFIC RAIL CORP
```

Tab Delimited
```
-----BEGIN PRIVACY-ENHANCED MESSAGE-----
Proc-Type: 2001,MIC-CLEAR
Originator-Name: keymaster@town.hall.org
Originator-Key-Asymmetric:
 MFkwCgYEVQgBAQICAgADSwAwSAJBALeWW4xDV4i7+b6+UyPn5RtObb1cJ7VkACDq
 pKb9/DClgTKIm08lCfoilvi9Wl4SODbR1+1waHhiGmeZO8OdgLUCAwEAAQ==
MIC-Info: RSA-MD5,RSA,
 Xzk7NMlOzKoaTGXXDCmTTNNw5KQDPpNzSUd3yWZzICc51rTLO3IEHKbx4vtrIKgW
 ip5g590o8EUUX2DzdcLq+w==

<IMS-DOCUMENT>0000918983-94-000005.txt : 19940221
<IMS-HEADER>0000918983-94-000005.hdr.sgml : 19940221
ACCESSION NUMBER:		0000918983-94-000005
CONFORMED SUBMISSION TYPE:	SC 13G
CONFIRMING COPY:	
PUBLIC DOCUMENT COUNT:		1
FILED AS OF DATE:		19940218

SUBJECT COMPANY:

    COMPANY DATA:	
            COMPANY CONFORMED NAME:			APPLE COMPUTER INC
            CENTRAL INDEX KEY:			0000320193
            ...
```

Note that privacy enhanced message only occurs in some tab delimited submissions.

### Examples

Parsed Header Metadata
```
    "accession-number": "0001096906-23-000016",
    "type": "10-K",
    "public-document-count": "44",
    "period": "20211231",
    "filing-date": "20230105",
    "date-of-filing-date-change": "20230105",
    "filer": {
        "company-data": {
            "conformed-name": "IA Energy Corp.",
            "cik": "0001673431",
            "assigned-sic": "2800",
            "irs-number": "811002497",
            "state-of-incorporation": "WY",
            "fiscal-year-end": "1231"
        },
        ...
}
```

Parsed Document Metadata
```
...
{
    "type": "XML",
    "sequence": "41",
    "filename": "report.css",
    "description": "IDEA: XBRL DOCUMENT",
    "secsgml_size_bytes": 2685,
    "secsgml_start_byte": "0001316864",
    "secsgml_end_byte": "0001319549"
},
...
```

## Document Metadata
Document metadata refers to the data in between the `<DOCUMENT>` and `<TEXT>` tags in the original SGML file. This metadata includes type (e.g. 10-K), sequence, filename, and description. Filename is missing for earlier entries, which rely on sequence.

```
<DOCUMENT>
<TYPE>4
<SEQUENCE>1
<FILENAME>primarydocument.xml
<DESCRIPTION>PRIMARY DOCUMENT
<TEXT>
```

### SEC Corpus by filetype
Total size: 16.7tb

| File Type | Total Size (Bytes) | File Count | Average Size (Bytes) |
|-----------|-------------------|------------|---------------------|
| htm | 7,556,829,704,482 | 39,626,124 | 190,703.23 |
| xml | 5,487,580,734,754 | 12,126,942 | 452,511.5 |
| jpg | 1,760,575,964,313 | 17,496,975 | 100,621.73 |
| pdf | 731,400,163,395 | 279,577 | 2,616,095.61 |
| xls | 254,063,664,863 | 152,410 | 1,666,975.03 |
| txt | 248,068,859,593 | 4,049,227 | 61,263.26 |
| zip | 205,181,878,026 | 863,723 | 237,555.19 |
| gif | 142,562,657,617 | 2,620,069 | 54,411.8 |
| json | 129,268,309,455 | 550,551 | 234,798.06 |
| xlsx | 41,434,461,258 | 721,292 | 57,444.78 |
| xsd | 35,743,957,057 | 832,307 | 42,945.64 |
| fil | 2,740,603,155 | 109,453 | 25,039.09 |
| png | 2,528,666,373 | 119,723 | 21,120.97 |
| css | 2,290,066,926 | 855,781 | 2,676.0 |
| js | 1,277,196,859 | 855,781 | 1,492.43 |
| html | 36,972,177 | 584 | 63,308.52 |
| xfd | 9,600,700 | 2,878 | 3,335.89 |
| paper | 2,195,962 | 14,738 | 149.0 |
| frm | 1,316,451 | 417 | 3,156.96 |

### Graphs
![img](../document_plots/extensions_over_time.png)
![img](../document_plots/extensions_by_count_pie.png)
![img](../document_plots/extensions_by_size_pie.png)

[Link to the Data](../document_csvs/)

## Header Metadata

We now turn to standardizing the key value pairs of the header metadata. This is difficult as we need both mappings between keys in tab and tag delimited as well as in values.

e.g. "1934 Act" - > "34 Act"