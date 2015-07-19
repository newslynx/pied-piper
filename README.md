ykk
===

A plugin for [zip.js](http://gildas-lormeau.github.io/zip.js/) to take a variety of json data formats and turn them into a zipped archive of text or csv data.

#### [View demo](http://newslynx.github.io/ykk)

This plugin creates a new function on `zip` called `zipMultiple`. Below is an example usage a walkthrough of the options.

### Example configuration

````html
<!-- html -->
<body>
	<a id="download-button">Download</a>
	<script src="js/dsv.min.js"></script> <!-- You can load either dsv or the full d3 library -->
	<script src="js/thirdparty/zip.js"></script>
	<script src="js/ykk.js"></script>

	<!-- js -->
	<script>
		var in_data = {
			fruits:  [{color: 'red', kind: 'apple'},{color: 'yellow', kind: 'banana'}],
			veggies: [{color: 'green', kind: 'kale'},{color: 'green', kind: 'broccolli', nickname: 'lame kale'}]
		};

		var key_crossover = {
			fruits: 'Fruit list',
			veggies: 'Veggie list'
		};

		zip.zipMultiple(in_data, 'csv', function(zippedBlob, zippedBlobHref) {
			// Set the URL as the href of the download button
			// This might not be a good solution for Safari
			var DOM_download_btn = document.getElementById('download-button');
			DOM_download_btn.href = zippedBlobHref;
			// Set the name of the file to download
			DOM_download_btn.download = 'newslynx-data.zip';
		}, key_crossover);
	</script>
</body
````



#### *zipMultiple(files, outputFormat, callback[, prettyKeys])*

##### `files`

Your data object. Its format can be: 

* a list of either objects or strings
* a dictionary whose keys are lists 

YKK will transform your data into an object of with a `key` and a `values` key. The `key` wil be the file name that's written out. For lists, the `key` be `'file-'+index`. For instance, this list:

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

has the following signature `(zippedBlob, zippedBlobHref)`. You can set the `href` of your download button link to `zippedBlobHref`. If you want to do more stuff to the zipped blob, you have access to that as the first argument passed in. See above for an example configuration

##### `prettyKeys`

If you don't want to use the direct keys as your file names, you can set up a crossover dictionary and pass that in as an optional fourth argument. See example configuration above.

