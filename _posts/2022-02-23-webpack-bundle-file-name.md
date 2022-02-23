---
layout: single
title: webpack4 bundle file name
date: 2022-02-23 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2022-02-22-http-auth/sign-in.png"
    image: "/assets/images/2022-02-22-http-auth/sign-in.png"
categories:
- IT
tags: [webpack]
---

1. 설정 방법
- 반드시 configure 아래에 output을 넣어야 한다.
  
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

2. filename과 chunkFilename의 차이
	1. filename
		1. entry file 이름
		2. 예: runtime-main.%@#$.js
		3. option 중 [hash]만 사용 가능. 하지만 react eject를 하면 나오는 설정에서는 [contenthash:8]로 되어 있다...)
	2. chunkFilename
		1.  webpack에서 비동기적으로 로드할 파일 이름
3. option
	1. id: module id. module이 추가되는 순서대로 증가한다
	2. name
		1. module name.
		2. 따로 module의 이름을 붙여주지 않았다면 id와 같다
	5. hash
		1. 전체 소스코드를 기준으로 한다. 따라서 일부만 변경되어도 전체 hash값이 바뀐다.
		2. 전체 소스코드를 기준으로 하기 때문에, 한 빌드 시점의 output file의 hash값은 모두 동일하다.
		3. new Date().getTime() 등을 파일명에 추가해주어 파일명을 바꿔줘도 hash값이 바뀐다. 아마도 webpack 설정 파일에 입력되는 값이 변경되니 그런듯
		4. webpack doc에 설명 간 모순이 있다. module id의 hash가 아니라, 매번 build 시점마다 hash를 새로 만들고, 각 build 시점마다의 hash값은 동일하다. 
		5. 공식 문서에는 hash는 매 빌드시마다 바뀐다고 하지만, 그렇지 않다.
	6. chunkhash
		1. 오로지 해당 chunk file의 content를 기준으로 hash
		2. unstable하다. content의 내용이 변경되지 않아도 hash가 변경될 수 있다. 왜냐면 chunk를 나누는 기준은 build 할 때마다 바뀔 수 있다(https://github.com/webpack/webpack/issues/7179)
	7. contenthash
		1. 오로지 해당 chunk file의 extracted 된 content를 기준으로 hash. 주석, 변수명 변경 등은 hash 결과에 영향을 미치지 않는다.
		2. unstable(chunkhash와 같은 이유)
	8. hash slice
		1. hash, contenthash, chunkhash의 length 조작
		2. 예
			1. hash를 앞 8자만 사용
			2. bundle.[hash:8].js
4. 그 외
	1.  function
		1. arrow function 가능
	2. template literals
		1. new Date().getTime() 등 js code 삽입 가능
	3. 예

    ```javascript
    filename: ({ chunk: {name, chunkname} }) => {
            const str = '' 
            return `static/js/[name].[chunkhash].${new Date().getTime()}.js`
        },
    ```

	4. inline style은? css로 만들어지며, 파일명은 output.filename과 동일한 룰로 만들어진다.
	5. build output 경로는? 물론 지정해줘야 한다.
	6. webpack의 output filename의 default 설정은 node_modules/webpack/lib/webpackOptionsDefaults.js 에서 확인
	7. create-react-app의 기본 설정은 이렇다. (node_modules/react-scripts/config/webpack.config.js)

    ```javascript
    filename: isEnvProduction
            ? 'static/js/[name].[contenthash:8].js'
            : isEnvDevelopment && 'static/js/bundle.js',
    chunkFilename: isEnvProduction
    ? 'static/js/[name].[contenthash:8].chunk.js'
    : isEnvDevelopment && 'static/js/[name].chunk.js',
    ```

5. 그래서 나의 최종 설정은

![final](/assets/images/2022-02-23-webpack-bundle-file-name/final.png)

-------

# TEST(참고)

## 1. 성공

date time 적용

hash가 build 할 때마다 hash가 바뀜

```javascript
filename: `[name].[id].[hash].${new Date().getTime()}.js`,
chunkFilename: [name].[id].[hash].${new Date().getTime()}.chunk.js
```

![1](/assets/images/2022-02-23-webpack-bundle-file-name/test1.png)

## 2. 실패
hash 안바뀜. 그대로!

```javascript
filename: `[name].[id].[hash].js`,
chunkFilename: `[name].[id].[hash].chunk.js
```

![2](/assets/images/2022-02-23-webpack-bundle-file-name/test1.png)

## 3. 실패
hash 안바뀜!

![3](/assets/images/2022-02-23-webpack-bundle-file-name/test3.png)


## 4. 실패
hash 안 바뀜!

![4](/assets/images/2022-02-23-webpack-bundle-file-name/test4.png)


## 5. 성공
* getTime을 추가하자
* hash 바뀜
* chunkhash 안 바뀜
* contenthash 안 바뀜

![5](/assets/images/2022-02-23-webpack-bundle-file-name/test5.png)

## 6. 실패(파일명은 다르긴 하지만, contenthash는 그대로 유지됨)(채택)

```javascript
filename: `[name].[id].[hash].${new Date().getTime()}.js`,
chunkFilename: `[name].[id].[contenthash].${new Date().getTime()}.chunk.js`
```

![6](/assets/images/2022-02-23-webpack-bundle-file-name/test6.png)

![7](/assets/images/2022-02-23-webpack-bundle-file-name/test7.png)

![8](/assets/images/2022-02-23-webpack-bundle-file-name/test8.png)




## 7. 이번엔?

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
		- webpack 5 지원 상황: https://github.com/facebook/create-react-app/pull/11201
	- hash와 chunkhash의 목적: https://stackoverflow.com/questions/35176489/what-is-the-purpose-of-webpack-hash-and-chunkhash
	- hash, chunkhash, contenthash 중 무엇을 사용해야 하나?: https://stackoverflow.com/questions/59194365/webpack-4-hash-and-contenthash-and-chunkhash-when-to-use-which

20210908
