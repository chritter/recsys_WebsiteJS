# Setup for JS Modification of STC website

* This setup below requires that an API is running as indicated in the JS
ccode below. API can be setup via the repo at https://gitlab.k8s.cloud.statcan.ca/recsys/webapi.


## Ia. Chrome Setup

Run `./start_chrome.sh` and install the following chrome extensions:

* Requestly (https://app.requestly.in/rules#edit/Script_1571227708569)
* Content Security Policy Override
* Access-Control-allow-origin

## Ib. Chrome Setup - REquestly setup

In requestly add the following code in the Javascript window/custom code:

### 1. Rule

Create a window:
```
url = window.location.href
if (sessionStorage.getItem("urls")) {
  var urls = JSON.parse(sessionStorage.getItem("urls"));
  urls.push(url);
  sessionStorage.setItem("urls", JSON.stringify(urls));
} else {
  var urls = [url];
  sessionStorage.setItem("urls", JSON.stringify(urls));
}
```
Create second code window:
```
async function postData(url = '', data = {}) {
  const response = await fetch(url, {
    method: 'POST',
    mode: 'cors',
    cache: 'no-cache',
    credentials: 'same-origin',
    headers: {
      'Content-Type': 'application/json'
    },
    redirect: 'follow',
    referrer: 'no-referrer',
    body: JSON.stringify(data)
  });
  return await response.json();
}
const data = postData('http://127.0.0.1:8000/predict_next', { item_list: urls});
data.then(function(result) { 
    recom = result; 
});
```
Uses fetch function from https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
Recommendendet urls are stored in the recom list object.

### 2. Rule


JS for the website embededded results
```
.rec_box {
        align-items: left; /* Use another value to see the result */
        width: 100%;
        height: 200px;
        background-color: #D8D8D8;
    	font-family:sans-serif;
      }
.rec_title {
    text-align: left;
    color:#787878;
    font-size:200%;
	height: 30px;
  	clear:both;
	margin: 15px;

    }
.rec_link {
    text-align: left;
    color:black;
    font-size:100%;
  	clear:both;
    margin: 15px;
    }

```

For embedding the links in the website, before the footer (with id wb-info):
```
var footer = document.getElementById('wb-info');
var div_box = document.createElement("div")
div_box.setAttribute('class', 'rec_box');
var div_title = document.createElement("div")
div_title.setAttribute('class', 'rec_title');
var t = document.createTextNode("Similar visitors also liked");  
div_title.appendChild(t)
div_box.appendChild(div_title)
var eleLi = document.querySelectorAll('[role="main"]');  
eleLi[0].appendChild(div_box)
```

### 3. Rule

Create new html elments as href links dependent on the lenght
of the object returned from the API.

```
ol = document.createElement("ol")
div_box.appendChild(ol)
var ii;
for (ii = 0; ii < recom['next_item'].length ; ii++) {
  li0 = document.createElement("li")
  a_link0 = document.createElement("a")
  a_link0.setAttribute('href',recom['next_item'][ii]);
  var link0 = document.createTextNode(recom['next_item'][ii]);  
  a_link0.appendChild(link0)
  li0.appendChild(a_link0) 
  ol.appendChild(li0)
}
```


One can test the javascriopt with the browser 
capabilities via inspect/Console functionality.

## II. Tests
* Visit STC websites and observe any errors. Can check API dashboard to see API calls. 
Use the other repo code for that.

## Other

This code can allow us to download data from  within JS.

```
function download(dataurl, filename) {
  var a = document.createElement("a");
  a.href = dataurl;
  a.setAttribute("download", filename);
  a.click();
}
var Path = window.location;
download("data:text/html,"+Path, "helloWorld.txt");
```

Create random ids within JS

```
Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15);
```