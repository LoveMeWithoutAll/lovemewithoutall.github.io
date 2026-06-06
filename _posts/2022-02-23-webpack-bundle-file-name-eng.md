---
layout: single
title: webpack4 bundle file name
date: 2022-02-23 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2022-02-23-webpack-bundle-file-name/final.png"
    image: "/assets/images/2022-02-23-webpack-bundle-file-name/final.png"
categories:
- IT
tags: [webpack]
---

1. How to configure
- You must place `output` under `configure`.
  
```javascript
module.exports = {   
    configure: {
    	output: {
    		filename: `[name].[hash].js`,
    		chunkFilename:  `[name].[hash].chunk.js
    	}
    }
}
```

2. The difference between filename and chunkFilename
	1. filename
		1. The name of the entry file
		2. Example: runtime-main.%@#$.js
		3. Among the options, only [hash] can be used. However, in the config that comes out when you eject from react, it is set to [contenthash:8]...)
	2. chunkFilename
		1.  The name of the file that webpack loads asynchronously
3. options
	1. id: module id. It increases in the order that modules are added
	2. name
		1. module name.
		2. If you didn't assign a separate name to the module, it is the same as the id
	5. hash
		1. It is based on the entire source code. Therefore, even if only a part changes, the whole hash value changes.
		2. Since it is based on the entire source code, all the hash values of the output files at a single build point are identical.
		3. Even if you change the filename by adding something like new Date().getTime() to it, the hash value changes. Probably because the value entered into the webpack config file changes.
		4. There is a contradiction within the webpack doc's explanation. It's not the hash of the module id; rather, a hash is newly generated at each build point, and the hash values at each build point are identical.
		5. The official docs say the hash changes on every build, but it doesn't.
	6. chunkhash
		1. A hash based solely on the content of that chunk file
		2. It is unstable. The hash can change even if the content has not changed. This is because the criteria for splitting chunks can change with every build (https://github.com/webpack/webpack/issues/7179)
	7. contenthash
		1. A hash based solely on the extracted content of that chunk file. Comments, variable name changes, etc. do not affect the hash result.
		2. unstable (for the same reason as chunkhash)
	8. hash slice
		1. Manipulating the length of hash, contenthash, chunkhash
		2. Example
			1. Using only the first 8 characters of the hash
			2. bundle.[hash:8].js
4. Other
	1.  function
		1. arrow functions are possible
	2. template literals
		1. You can insert js code such as new Date().getTime()
	3. Example

    ```javascript
    filename: ({ chunk: {name, chunkname} }) => {
            const str = '' 
            return `static/js/[name].[chunkhash].${new Date().getTime()}.js`
        },
    ```

	4. What about inline styles? They are turned into CSS, and the filename is created with the same rule as output.filename.
	5. What about the build output path? Of course, you have to specify it.
	6. You can check webpack's default output filename setting in node_modules/webpack/lib/webpackOptionsDefaults.js
	7. The default setting of create-react-app is like this. (node_modules/react-scripts/config/webpack.config.js)

    ```javascript
    filename: isEnvProduction
            ? 'static/js/[name].[contenthash:8].js'
            : isEnvDevelopment && 'static/js/bundle.js',
    chunkFilename: isEnvProduction
    ? 'static/js/[name].[contenthash:8].chunk.js'
    : isEnvDevelopment && 'static/js/[name].chunk.js',
    ```

5. So my final configuration is

![final](/assets/images/2022-02-23-webpack-bundle-file-name/final.png)

-------

# TEST (for reference)

## 1. Success

date time applied

the hash changes on every build

```javascript
filename: `[name].[id].[hash].${new Date().getTime()}.js`,
chunkFilename: [name].[id].[hash].${new Date().getTime()}.chunk.js
```

![1](/assets/images/2022-02-23-webpack-bundle-file-name/test1.png)

## 2. Failure
The hash doesn't change. It stays the same!

```javascript
filename: `[name].[id].[hash].js`,
chunkFilename: `[name].[id].[hash].chunk.js
```

![2](/assets/images/2022-02-23-webpack-bundle-file-name/test1.png)

## 3. Failure
The hash doesn't change!

![3](/assets/images/2022-02-23-webpack-bundle-file-name/test3.png)


## 4. Failure
The hash doesn't change!

![4](/assets/images/2022-02-23-webpack-bundle-file-name/test4.png)


## 5. Success
* Let's add getTime
* hash changes
* chunkhash doesn't change
* contenthash doesn't change

![5](/assets/images/2022-02-23-webpack-bundle-file-name/test5.png)

## 6. Failure (the filename does differ, but the contenthash stays the same) (adopted)

```javascript
filename: `[name].[id].[hash].${new Date().getTime()}.js`,
chunkFilename: `[name].[id].[contenthash].${new Date().getTime()}.chunk.js`
```

![6](/assets/images/2022-02-23-webpack-bundle-file-name/test6.png)

![7](/assets/images/2022-02-23-webpack-bundle-file-name/test7.png)

![8](/assets/images/2022-02-23-webpack-bundle-file-name/test8.png)




## 7. How about this time?

```javascript
filename: `[name].[id].[hash].${new Date().getTime()}.js`,
chunkFilename: `[name].[id].[hash].${new Date().getTime()}.chunk.js`
```

![9](/assets/images/2022-02-23-webpack-bundle-file-name/test9.png)

![10](/assets/images/2022-02-23-webpack-bundle-file-name/test10.png)

![11](/assets/images/2022-02-23-webpack-bundle-file-name/test11.png)

----------

# reference

- output
	- filename: https://v4.webpack.js.org/configuration/output/#outputfilename
	- chunkFilename: https://v4.webpack.js.org/configuration/output/#outputchunkfilename
	- 
|    Template   |                              Description                             |
|:-------------:|:--------------------------------------------------------------------:|
| [hash]        | The hash of the module identifier                                    |
| [contenthash] | the hash of the content of a file, which is different for each asset |
| [chunkhash]   | The hash of the chunk content                                        |
| [name]        | The module name                                                      |
| [id]          | The module identifier                                                |
| [query]       | The module query, i.e., the string following ? in the filename       |
| [function]    | The function, which can return filename [ string ]                   |

- mini-css-extract-plugin
	- webpack doc: https://v4.webpack.js.org/plugins/mini-css-extract-plugin/#root
		- filename: https://v4.webpack.js.org/plugins/mini-css-extract-plugin/#filename
		- chunkFilename: https://v4.webpack.js.org/plugins/mini-css-extract-plugin/#chunkfilename
	- git repo: https://github.com/webpack-contrib/mini-css-extract-plugin
		- changeLog: https://github.com/webpack-contrib/mini-css-extract-plugin/blob/master/CHANGELOG.md
	- create-react-app
		- webpack 5 support status: https://github.com/facebook/create-react-app/pull/11201
	- The purpose of hash and chunkhash: https://stackoverflow.com/questions/35176489/what-is-the-purpose-of-webpack-hash-and-chunkhash
	- Which of hash, chunkhash, contenthash should you use?: https://stackoverflow.com/questions/59194365/webpack-4-hash-and-contenthash-and-chunkhash-when-to-use-which

20210908
