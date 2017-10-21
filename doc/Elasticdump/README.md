### Elasticdump ElasticSearch 数据导出工具 ###

elasticdump实现导出一个索引到另一个位置的索引，或者是实现索引到文件，实现备份和迁移，详见[github：](https://github.com/taskrabbit/elasticsearch-dump)

#### 安装 ####
1、安装NodeJS，这里不再shuoming

2、npm install elasticdump -g

#### 示例 ####

	elasticdump \
	  --input=http://production.es.com:9200/my_index \
	  --output=http://staging.es.com:9200/my_index \
	  --type=analyzer
	elasticdump \
	  --input=http://production.es.com:9200/my_index \
	  --output=http://staging.es.com:9200/my_index \
	  --type=mapping
	elasticdump \
	  --input=http://production.es.com:9200/my_index \
	  --output=http://staging.es.com:9200/my_index \
	  --type=data

# Backup index data to a file:

	elasticdump \
	  --input=http://production.es.com:9200/my_index \
	  --output=/data/my_index_mapping.json \
	  --type=mapping
	elasticdump \
	  --input=http://production.es.com:9200/my_index \
	  --output=/data/my_index.json \
	  --type=data

# Backup and index to a gzip using stdout:

	elasticdump \
	  --input=http://production.es.com:9200/my_index \
	  --output=$ \
	  | gzip > /data/my_index.json.gz

# Backup the results of a query to a file

	elasticdump \
	  --input=http://production.es.com:9200/my_index \
	  --output=query.json \
	  --searchBody '{"query":{"term":{"username": "admin"}}}'

#### 参数说明 ####

    	--input
	                    Source location (required)
		--input-index
	                    Source index and type
	                    (default: all, example: index/type)
		--output
	                    Destination location (required)
		--output-index
	                    Destination index and type
	                    (default: all, example: index/type)
		--limit
	                    How many objects to move in batch per operation
	                    limit is approximate for file streams
	                    (default: 100)
		--debug
	                    Display the elasticsearch commands being used
	                    (default: false)
		--quiet
	                    Suppress all messages except for errors
	                    (default: false)
		--type
	                    What are we exporting?
	                    (default: data, options: [analyzer, data, mapping])
		--delete
	                    Delete documents one-by-one from the input as they are
	                    moved.  Will not delete the source index
	                    (default: false)
		--searchBody
	                    Preform a partial extract based on search results
	                    (when ES is the input,
	                    (default: '{"query": { "match_all": {} }, "fields": ["*"], "_source": true }'))
		--headers
	                    Add custom headers to Elastisearch requests (helpful when
	                    your Elasticsearch instance sits behind a proxy)
	                    (default: '{"User-Agent": "elasticdump"}')
		--sourceOnly
	                    Output only the json contained within the document _source
	                    Normal: {"_index":"","_type":"","_id":"", "_source":{SOURCE}}
	                    sourceOnly: {SOURCE}
	                    (default: false)
		--ignore-errors
	                    Will continue the read/write loop on write error
	                    (default: false)
		--scrollTime
	                    Time the nodes will hold the requested search in order.
	                    (default: 10m)
		--maxSockets
	                    How many simultaneous HTTP requests can we process make?
	                    (default:
	                      5 [node <= v0.10.x] /
	                      Infinity [node >= v0.11.x] )
		--timeout
	                    Integer containing the number of milliseconds to wait for
	                    a request to respond before aborting the request. Passed
	                    directly to the request library. Mostly used when you don't
	                    care too much if you lose some data when importing
	                    but rather have speed.
		--offset
	                    Integer containing the number of rows you wish to skip
	                    ahead from the input transport.  When importing a large
	                    index, things can go wrong, be it connectivity, crashes,
	                    someone forgetting to `screen`, etc.  This allows you
	                    to start the dump again from the last known line written
	                    (as logged by the `offset` in the output).  Please be
	                    advised that since no sorting is specified when the
	                    dump is initially created, there's no real way to
	                    guarantee that the skipped rows have already been
	                    written/parsed.  This is more of an option for when
	                    you want to get most data as possible in the index
	                    without concern for losing some rows in the process,
	                    similar to the `timeout` option.
	                    (default: 0)
		--noRefresh
	                    Disable input index refresh.
	                    Positive:
	    	            	1. Much increase index speed
	    	                2. Much less hardware requirements
	                    Negative:
	                      1. Recently added data may not be indexed
	                    Recommended to use with big data indexing,
	                    where speed and system health in a higher priority
	                    than recently added data.
		--inputTransport
	                    Provide a custom js file to us as the input transport
		--outputTransport
	                    Provide a custom js file to us as the output transport
		--toLog
	                    When using a custom outputTransport, should log lines
	                    be appended to the output stream?
	                    (default: true, except for `$`)
		--transform
	                    A javascript, which will be called to modify documents
	                    before writing it to destination. global variable 'doc'
	                    is available.
	                    Example script for computing a new field 'f2' as doubled
	                    value of field 'f1':
	                        doc._source["f2"] = doc._source.f1 * 2;
	                    May be used multiple times.
	                    Additionally, transform may be performed by a module. See [Module Transform](#module-transform) below.
		--awsChain
	                    Use [standard](https://aws.amazon.com/blogs/security/a-new-and-standardized-way-to-manage-credentials-in-the-aws-sdks/) location and ordering for resolving credentials including environment variables, config files, EC2 and ECS metadata locations
	                    _Recommended option for use with AWS_ 
		--awsAccessKeyId
		--awsSecretAccessKey
	                    When using Amazon Elasticsearch Service proteced by
	                    AWS Identity and Access Management (IAM), provide
	                    your Access Key ID and Secret Access Key.
	                    --sessionToken can also be optionally provided if using temporary credentials
		--awsIniFileProfile
	                    Alternative to --awsAccessKeyId and --awsSecretAccessKey,
	                    loads credentials from a specified profile in aws ini file.
	                    For greater flexibility, consider using --awsChain
	                    and setting AWS_PROFILE and AWS_CONFIG_FILE
	                    environment variables to override defaults if needed
		--awsIniFileName
	                    Override the default aws ini file name when using --awsIniFileProfile
	                    Filename is relative to ~/.aws/ 
	                    (default: config)
		--help
	                    This page
