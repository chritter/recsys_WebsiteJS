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

```
Uses fetch function from https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch

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