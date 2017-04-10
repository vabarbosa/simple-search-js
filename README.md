# SimpleSearchJS

SimpleSearchJS is a JavaScript module that makes it even easier to consume the [Simple Search Service](https://developer.ibm.com/clouddataservices/2016/01/21/introducing-simple-faceted-search-service/) API and enhance your web page.

Once installed the module can be used to query the service and then have the responses automatically parsed, formatted, and placed onto the web page.
  
Apart from querying the Simple Search Service and returning the response, the SimpleSearchJS module includes some additional UI functionality.

* __Search requests from `input`__: The module can be attached to any text `input` tag and automatically perform search requests when the ENTER key pressed (using the value of the `input`).

* __Search button__: An element (e.g., `button`, `link`, etc) can be provided which would act as the search button and trigger a search. Alternatively, the module can be configured to automatically create the search button and append besides the `input`.

* __Facets as list__: The search response can be parsed and facets formatted as an unordered list (i.e., `ul`) and the HTML fragment automatically inserted onto the web page.

* __Results as table__: The search response can be parsed and results formatted as an HTML `table` (i.e., `ul`) and the fragment automatically inserted onto the web page.

* __Results as list__: The search response can be parsed and results formatted as an unordered list (i.e., `ul`) and the HTML fragment automatically inserted onto the web page.


# Install

Download and include [`simplesearch.js`](https://github.com/ibm-cds-labs/simple-search-js/blob/master/simplesearch.js) in your HTML file:

```
<script type="text/javascript" src="simplesearch.js"></script>
```


# Usage

The module can be initialized via JavaScript or using HTML data attributes:  

* __HTML data attributes__

	```
	<input type="text" data-simple-search="http://a-simple-search-service.com"
	        data-search-results="#results .tablewrapper"
	        data-search-list=".resultsListWrapper"
	        data-search-facets="#facetsWrapper"
	        data-search-deeplinking="true">
	```

	__data-simple-search__ - (_Required_) the url for the Simple Search Service to connect to  
	__data-search-results__ - (_Optional_) the CSS selector for the element where the search results HTML (`table`) fragment should be inserted  
	__data-search-list__ - (_Optional_) the CSS selector for the element where the search results HTML (`ul`) fragment should be inserted  
	__data-search-facets__ - (_Optional_) the CSS selector for the element where the search facets HTML (`ul`) fragment should be inserted  
	__data-search-deeplinking__ - (_Optional_) `true` to enable deep linking of search queries
	
* __Javascript__

	```
	var simplesearch = new SimpleSearch(serviceUrl, callbacks, selectors, config);
	```

	__serviceUrl__ - (_Optional_) the url for the Simple Search Service to connect to  
	__callbacks__ - (_Optional_) a JavaScript object containing the callback functions to call  (e.g, _onSuccess_, _onFail_, etc)  
	__selectors__ - (_Optional_) a JavaScript object containing the CSS selectors (e.g., _resultsTable_, _facetsList_, etc)  
	__config__ - (_Optional_) a JavaScript object containing configuration settings (e.g., _deepLinking_, etc)
  
When the `DOMContentLoaded` event is fired the SimpleSearchJS will check the page for the HTML `data-*` attributes. If none is found or the JavaScript file is added to the page after the event or the `input` (with `data-*` attributes) is added to the page after the event then the module would need to be initialized via JavaScript (or `update` called).  

It is possible to use both approaches together. In such cases, for any parameters defined in both, the JavaScript value would supersede the data attribute value. The main difference between the two approaches is that callbacks are supported in JavaScript but not in the data attributes.

# Callbacks

* ### onBefore()  
	
	The callback is fired right before a search request is made.  
	
* ### onSuccess(results)  
	
	Fired when a search request completes successfully. The __results__ argument passed is a JSON object containing the response from the Simple Search Service plus a few additional fields:
	
	```
	{
	   "data": {},
	   "fields": [],
	   "facets": [],
	   "rest_uri": "",
	   "time": 170,
	   "paging": {
	   		"bookmarks": [],
	   		"hasMore": true
		}
	}
	```
		
	__data__ - JSON response from the Simple Search Service  
	__fields__ - an array of available fields and field type (e.g., string, number. etc)  
	__facets__ - an array of the faceted fields and field type  
	__rest_uri__ - the URL used to make the request (e.g., _http://a-simple-search-service.com/search?q=*:*_)  
	__time__ - the time (in ms) it took for the request to complete  
	__paging.bookmarks__ - an array of bookmark IDs  
	__paging.hasMore__ - `true` if there is more content to retrieve
		
	
* ### onFail(err)  
	
	The callback is made when a search request fails or cannot be made. The __err__ argument passed is a JSON object with an `error` and `reason` fields. For example:
	
	```
	{
	   "error": "bad_request",
	   "reason": "Page out of bounds: No next page"
	}
	```
		
# Selectors  

| Name | Type | Descriptions |
|---|---|---|
| inputField | string or HTML DOM element | _Optional_ - The search input field. It may be an `input` HTML DOM element or a CSS selector string for the `input` element. For example, "__#searchInput__" to select an `input` with an ID of _searchInput_ |
| searchButton | boolean, string, or HTML DOM element | _Optional_ - The search button. It may be an HTML DOM element, a CSS selector string for the element, or a boolean. If __true__ a search button is created and appended beside the `input`.
| resultsTable | string or HTML DOM element | _Optional_ - Where the search results HTML (`table`) fragment is to be inserted. An HTML DOM element or a CSS selector string for the element.
| resultsList | string or HTML DOM element | _Optional_ - Where the search results HTML (`ul`) fragment is to be inserted. An HTML DOM element or a CSS selector string for the element.
| facetsList | string or HTML DOM element | _Optional_ - Where the search results facets HTML (`ul`) fragment is to be inserted. An HTML DOM element or a CSS selector string for the element.
		
# Config  

| Name | Descriptions |
|---|---|
| deepLinking | `true` to allow deep linking of search queries |


# API

### search()  

Searching is automatically performed when the _ENTER_ key is pressed (with focus on the `input`) or when the search button is clicked. In addition, a search request can also be triggered programmatically:
	
```
simplesearch.search(queryString, limit);
```

__queryString__ - (_Required_) the query to perform (e.g., `*:*`, `city:Boston AND state:MA`, etc.).  
__limit__ - (_Optional_) maximum number of results to return. If omitted the number of results returned depends on the Simple Search Service configuration.  

### next()

Paging is supported by the module using a Next/Prev page pattern. To get the next page of results:

```
simplesearch.next();
```

### prev()

Paging is supported by the module using a Next/Previous page pattern. To get the previous page of results:

```
simplesearch.prev();
```

### update(inputId, callbacks, selectors, configs)

Change/update the SimpleSearch module attached to the `inputId` with the values of the provided parameters:

```
SimpleSearch("searchinput", callbackObj, null, updatedConfigs)
```

# Fragments

The HTML fragments generated by the module and inserted into the page are as follows:

### Search Results Table

```
<div class="simplesearch-results-table">
  <div class="simplesearch-count">
    <span>Showing x - y of z</span>
  </div>
  <div class="simplesearch-paging">
    <button class="simplesearch-prev">Prev</button>
    <button class="simplesearch-next">Next</button>
  </div>
  <table class="simplesearch-table">
    <thead>
      <tr>
        <th>field name</th>
        .
        .
        .
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><span>value</span></td>
        .
        .
        .
      </tr>
      .
      .
      .
    </tbody>
  </table>
</div>
```

### Search Result Unordered Lists

```
<div class="simplesearch-results-list">
  <div class="simplesearch-count">
    <span>Showing x of y</span>
  </div>
  <ul class="simplesearch-list">
    <li class="simplesearch-list-item">
      <dl>
        <dt class="simplesearch-field-*">field name</dt>
        <dd class="simplesearch-value-*">value</dd>
      </dl>
      .
      .
      .
    </li>
    .
    .
    .
  </ul>
  <div class="simplesearch-paging">
    <button class="simplesearch-more">More</button>
  </div>
</div>
```

### Facets Unordered Lists

```
<div class="simplesearch-facets-list">
  <div>
    <h4 class="simplesearch-facet-key">facet name</h4>
    <ul class="simplesearch-facet-value-list">
      <li class="simplesearch-facet-value">
        <span class="simplesearch-facet-value-name" role="button" data-search-query="facetname:facetvalue">facet value</span>
        <span class="simplesearch-facet-value-count">(facet count)</span>
      </li>
      .
      .
      .
    </ul>
  </div>
  .
  .
  .
</div>
```

# Demo  

Examples can be found in the [`/demo`](https://github.com/ibm-cds-labs/simple-search-js/tree/master/demo) folder of the respository.  

# Frameworks

The SimpleSearchJS module is vanilla JavaScript and does not require any additional libraries. It can therefore be easily intergrated.  

However, for those preferring it, a jQuery plugin is also available. The jQuery plugin ([`jquery.simplesearch.js`](https://github.com/ibm-cds-labs/simple-search-js/blob/master/jquery.simplesearch.js)) is simply a wrapper around the vanilla JavaScript module ([`simplesearch.js`](https://github.com/ibm-cds-labs/simple-search-js/blob/master/simplesearch.js)).  
