# Vite for WordPress

[Vite](https://vitejs.dev) integration for WordPress plugins and themes development.

## Usage

### Plugin/theme

Add the composer dependency:

```sh
composer require kucrut/vite-for-wp
```

If your plugin/theme doesn't use composer, feel free to copy [the main file](php/src/vite-for-wp.php) and require it.

Enqueue the script:

```php
<?php

use Kucrut\Vite;

add_action( 'wp_enqueue_scripts', function (): void {
	Vite\enqueue_asset(
		__DIR__ . 'js/dist',
		'src/main.ts',
		[
			'handle' => 'my-script-handle',
			'dependencies' => [ 'wp-components', 'some-script-handle' ], // Optional script dependencies. Defaults to empty array.
			'css-dependencies' => [ 'wp-components', 'some-style-handle' ], // Optional style dependencies. Defaults to empty array.
			'css-media' => 'all', // Optional.
			'css-only' => false, // Optional. Set to true to only load style assets in production mode.
			'in-footer' => true, // Optional. Defaults to false.
		]
	);
} );
```

To only register the asset, use `Vite\register_asset()`. It accepts same parameters as `Vite\enqueue_asset()` and returns an array of scripts and styles handles that you can enqueue later using `wp_enqueue_script()` and `wp_enqueue_style()`. Please note that style assets are only registered in production mode because in development mode, they will be automatically loaded by Vite.

### JavaScript

From your JS package, add the dependencies:

```sh
npm add -D vite @kucrut/vite-for-wp
```

Create `vite.config.js`:

```js
import create_config from '@kucrut/vite-for-wp';

export default create_config( 'js/src/main.ts', 'js/dist' );
```

Pass a [configuration object](https://vitejs.dev/config/) as the third parameter if you need to add plugins, use https, etc:

```js
import { readFileSync } from 'node:fs';
import { resolve } from 'node:path';
import create_config from '@kucrut/vite-for-wp';
import react from '@vitejs/plugin-react';

export default create_config( 'js/src/main.ts', 'js/dist', {
	plugins: [ react() ],
	server: {
		host: 'mydomain.com',
		https: {
			cert: readFileSync( 'path/to/cert.pem' ),
			key: readFileSync( 'path/to/key.pem' ),
		},
	},
} );
```

Lastly, add `dev` and `build` scripts to your `package.json`:

```json
{
	"scripts": {
		"build": "vite build",
		"dev": "vite"
	}
}
```

You can now run `npm run dev` when developing your plugin/theme and run `npm run build` to build the production assets.

## Limitations

Currently, this package doesn't provide HMR support for building editor blocks yet.

## License

[GPL v2](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html)
