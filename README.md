# flamework-chatterbox

Flamework libraries for working with the Who's On First chatterbox daemon.

## Important

Two things:

1. If you're not already familiar with how `Flamework` projects work this will probably won't make much sense.
2. The `libphp-predis` package (used by `flamework-redis`) does not play nicely with the built-in pubsub server that is part of go-whosonfirst-chatterbox so you'll need to make sure that you are routing requests through a copy of `redis-server` or equivalent.

## Install

First of all, you'll need to make sure you have a copy of `lib_redis.php` installed. This is available as part of the [flamwork-redis](https://github.com/whosonfirst/flamework-redis) package.

After that, just copy the [lib_chatterbox.php](www/include/lib_chatterbox.php) file in to your `include` directory.

## Configs

```	
	# START OF flamework-redis stuff (used by the chatterbox stuff, below)

	$GLOBALS['cfg']['redis_scheme'] = 'tcp';
	$GLOBALS['cfg']['redis_host'] = 'localhost';
	$GLOBALS['cfg']['redis_port'] = 6379;

	# END OF flamework-redis stuff

	# START OF chatterbox stuff / https://github.com/whosonfirst/go-whosonfirst-chatterbox

	$GLOBALS['cfg']['enable_feature_chatterbox'] = 1;

	$GLOBALS['cfg']['chatterbox_host'] = '127.0.0.1';
	$GLOBALS['cfg']['chatterbox_port'] = '6379';
	$GLOBALS['cfg']['chatterbox_channel'] = 'chatterbox';
	$GLOBALS['cfg']['chatterbox_destination'] = '...';

	# END OF chatterbox stuff
```

If you want to log API calls with `chatterbox` you will also copy the [lib_api_log_chatterbox.php](www/include/lib_api_log_chatterbox.php) file in to your `include` directory and then load it in your `$GLOBALS['cfg']['autoload_libs']` array (or equivalent).

For example:

```
        $GLOBALS['cfg']['autoload_libs'] = array_merge(array(
		'api_log_chatterbox',
	), $GLOBALS['cfg']['autoload_libs']);
```	

## Example

```
	loadlib("chatterbox");

	$data = array( ... );			# pretty much anything that can be json_encode() -ed

	$more = array(
		"host" => "example-1",		# defaults to gethostname()
		"application" => "example",	# defaults to $GLOBALS["cfg"]["site_name"],
		"context" => "example",		# defaults to $GLOBALS["cfg"]["environment"],
		"status" => "err",		# defaults to "ok"
		"status_code" => 420,		# defaults to 1
	);

	chatterbox_dispatch($data, $more)
```

Note that _where_ messages are dispatched to is controlled by global configs (defined above).

## See also

* https://github.com/whosonfirst/go-whosonfirst-chatterbox
* https://github.com/whosonfirst/flamework-redis
* https://github.com/whosonfirst/flamework-api
