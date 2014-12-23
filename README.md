ykk
===

An implementation of [zip.js]() to take a variety of json data formats and turn them into a zipped archive of text or csv data.

The code is commented but basically this script accepts both objects and lists through its one function `zipMultiple`.

##### [View demo](http://newslynx.github.io/ykk)

#### *zipMultiple(files, outputFormat, callback[, prettyKeys])*

##### `files`

Your data object. Its format can be: 

* a list of either objects or strings
* a dictionary whose keys are lists 

ykk will transform your data into an object of with a `key` and a `values` key. The `key` wil be the file name that's written out. For lists, the `key` be `'file-'+index`. For instance, this list:

````js
var data = [
	'The carbon in our apple pies billions upon billions cosmos. Extraplanetary Hypatia.',
	'Tendrils of gossamer clouds? Rogue stirred by starlight across the centuries cosmic ocean.'
]
````

Will become

````js
[
	{
		key: 'file-0',
		values: 'The carbon in our apple pies billions upon billions cosmos. Extraplanetary Hypatia.'
	},{
		key: 'file-1',
		values: 'Tendrils of gossamer clouds? Rogue stirred by starlight across the centuries cosmic ocean.'
	}
]
````

For dictionaries, the `key` will the key that object was under. For instance, this dictionary:

````js
var data = {
	fruits:  [{color: 'red', kind: 'apple'},{color: 'yellow', kind: 'banana'}],
	veggies: [{color: 'green', kind: 'kale'},{color: 'green', kind: 'brocolli', nickname: 'lame kale'}]
}
````

Will become: 

````js
[
	{
		key: 'fruits',
		values: [{color: 'red', kind: 'apple'},{color: 'yellow', kind: 'banana'}]
	},{
		key: 'veggies',
		values: [{color: 'green', kind: 'kale'},{color: 'green', kind: 'brocolli', nickname: 'lame kale'}]
	}
]
````

##### `outputFormat`

A string to determine how you want to write the data under `values`. It can be either `'txt'` or `'csv'`. 

* `'txt'` will do nothing if your list values are strings. If they are not strings, it will run `JSON.stringify` over them. 
* `'csv'` will run `dsv.csv.format` and will only work if your `values` is an array of objects. The result will be a string in comma-separated values format. Check out the [dsv](https://github.com/mbostock/dsv) documentation for more info.


##### `callback`

has the following signature `(zippedBlob)`. You'll want to run `createObjectURL` on it to return the zipped data in url format. See below for an example configuration

##### `prettyKeys`

If you don't want to use the direct keys as your file names, you can set up a crossover dictionary and pass that in as an optional fourth argument. See below.

````js
	// Some cross-browser cacheing of conversion functions
	var WINDOW = window,
			URL = WINDOW.webkitURL || WINDOW.mozURL || WINDOW.URL,
			createObjectURL = URL.createObjectURL; // Convert a compressed blob object to a data URL

	var in_data = {
		fruits:  [{color: 'red', kind: 'apple'},{color: 'yellow', kind: 'banana'}],
		veggies: [{color: 'green', kind: 'kale'},{color: 'green', kind: 'broccolli', nickname: 'lame kale'}]
	};

	var key_crossover = {
		fruits: 'Fruit list',
		veggies: 'Veggie list'
	}

	zipMultiple(in_data, 'csv', function(zippedBlob) {
		var zipped_blog_href = createObjectURL(zippedBlob);
		// Set the URl as the href of the download button
		// This might not be a good solution for Safari
		var DOM_download_btn = document.getElementById('download-button');
		DOM_download_btn.href = zipped_blog_href;
		// Set the name of the file to download
		DOM_download_btn.download = 'newslynx-data.zip';
	}, key_crossover);
````

