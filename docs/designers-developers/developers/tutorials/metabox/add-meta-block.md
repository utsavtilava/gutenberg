# Create Meta Block

With the meta field registered in the previous step, next you will create a new block used to display the field to the user. See the [Block Tutorial](../../../../../docs/designers-developers/developers/tutorials/block-tutorial/readme.md) for a deeper understanding of creating custom blocks.

For this block, you will use the TextControl component, this is similar to an HTML input text field. For additional components, see the list in the [editor package repository](https://github.com/WordPress/gutenberg/tree/master/packages/editor/src/components).


Attributes are the information displayed in blocks. As shown in the block tutorial, the source of attributes come from the text/html a user writes in the editor. For your meta block, the attribute will come from the post meta field.

By specifying the source of the attributes as `meta`, the Block Editor automatically handles the loading and storing of the data; no REST API or data functions are needed.

Replace the previous content in `myguten.js` with the following:

```js
var el = wp.element.createElement;
var registerBlockType = wp.blocks.registerBlockType;
var TextField = wp.components.TextControl;

registerBlockType("myguten/meta-block", {
	title: "Meta Block",
	icon: "smiley",
	category: "common",

	attributes: {
		blockValue: {
			type: "string",
			source: "meta",
			meta: "myguten_meta_block_field"
		}
	},

	edit: function(props) {
		var className = props.className;
		var setAttributes = props.setAttributes;

		function updateBlockValue( blockValue ) {
			setAttributes({ blockValue });
		}

	return el(
			"div",
			{ className: className },
			el( TextField, {
				label: "Meta Block Field",
				value: props.attributes.blockValue,
				onChange: updateBlockValue
			} )
		);
	},

	// No information saved to the block
	// Data is saved to postmeta via attributes
	save: function() {
		return null;
	}
});
```

**Important:** Before you test, you need to update the dependencies in the enqueue. Note the wp packages used above are: wp.element, wp.blocks, and wp.components. Each of these need to be included in the array of dependencies. Update the `myguten-meta-block.php` file so the enqueue function is:

```php
function gutentag_enqueue() {
	wp_enqueue_script( 'gutentag-script',
    	plugins_url( 'gutentag.js', __FILE__ ),
		array( 'wp-blocks', 'wp-element', 'wp-components' )
	);
}
add_action( 'enqueue_block_editor_assets', 'gutentag_enqueue' );
```

You can now edit a draft post and add a Meta Block to the post. You will see your field that you can type a value in. When you save the post, either as a draft or published, the post meta value will be saved too. You can verify by saving and reloading your draft, the form will still be filled in on reload.

![Meta Block](../../../../../docs/designers-developers/developers/tutorials/metabox/meta-block.png)

You can now use the post meta data in a template, or other area of your plugin using the `get_post_meta()` function.

You could also confirm the data is saved by checking the database table `wp_postmeta` and confirm the new post id contains the new field data.
