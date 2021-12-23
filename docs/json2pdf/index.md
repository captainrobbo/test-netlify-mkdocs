
# json2pdf

![Image]({{ static_root }}json2pdf/overview.png)

## What's it for?

Since about 2003, we have evolved a successful pattern to generating small documents from other peoples' data.   In essence the customer should export 'all the facts' for the report into a file; and we produce a small one-directory project exporting a function which transforms this file into a binary PDF document.  This architecture has benefits at many levels:

- The customer can see exactly how the output is working, and discuss changes, by running off sample data files.  So the reporting part of a project can be signed off and finished to the satisfaction of the business, sometimes while the customer's developers are still working on the data.
- There's a natural "test suite" consisting of sanitised sample data files produced by the customer
- The customer has freedom to call it as a web service in the early stages - leaving us free to investigate any data issues immediately in early development; or to deploy it internally on the architecture of their choice.
- PDF generation is completely separate from data extraction, so you can get our help on the former while keeping customer data confidential
- All other modes of generation - batches, direct execution in another process, and asynchronous generation - are still possible with the same code just by calling the inner function

We did this often enough that we have standardised the approach.

`json2pdf` is the standard way ReportLab deploys a solution which accepts [JSON](https://en.wikipedia.org/wiki/JSON) input and produces PDF output.


A ReportLab JSON2PDF deployment will typically consist of;

- Our `json2pdf` package, which includes various utilities and scripts
- Our `rlextra` and `reportlab` libraries, which make it easy to create a good-looking PDF
- Project-specific PDF templates, which implement the document-making function required. 

The templates can be developed by ReportLab, the client, or jointly.  We often build the first one, then mentor client development teams as they extend the reports.

In live server environments ReportLab typically runs json2pdf services via  `uwsgi` behind nginx.  


## Features of the web test harness
- availability to test different json files
- make optional edits to the sample json files via the UI
- debugging and validation utilities
- programmatically generate output from a terminal, for example using `curl` to `POST` a local json file;

`$ curl -i -X POST -H 'Content-Type: application/json' --output demo-kiids-sample.pdf --data @demo-kiids-sample.json https://demo-kiids.reportlab.com/cgi-bin/json2pdf.cgi`

## Features in production

As demonstrated by the above snippet, you simply POST a suitable JSON file at the endpoint, and a PDF document comes back!   

## Limitations
This is suitable for small-ish reports which can be generated without timeouts; if you need to post megabytes of input data, or the report is so long or detailed that it might take minutes to generate, a different approach is needed.


## Availability
If you have a hosted json2pdf ReportLab solution you will most likely have json2pdf running already on our servers.
If you don't, however, then please contact ReportLab (enquiries@reportlab.com) for further information on costs & authorisation.

## How to install and run locally

Authorised json2pdf users can use our pypi server to download the json2pdf package and install it via pip.

### Install and set up from ReportLab application repository
 `$ hg clone url`<br/>
 `$ python -mvirtualenv -p /path/to/desired/python .`<br/>
 `$ . bin/activate`<br/>
 `$ pip install -r requirements.txt`<br/>
 `$ setup-json2pdf`<br/>
### Running json2odf
 `$ wsgi-serve`
### View locally
 `http://localhost:8080`

## Demonstration of json2pdf in action
A [demonstration site](https://demo-kiids.reportlab.com/cgi-bin/json2pdf.cgi) is available for use.

![Image]({{ static_root }}json2pdf/demo-site-interface.png)
The user interface allows you to select available jsons & make optional edits.

<br/ >

![Image]({{ static_root }}json2pdf/demo-site-output.png)
Example output
