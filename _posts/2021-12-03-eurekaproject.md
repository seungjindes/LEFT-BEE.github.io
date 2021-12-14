---
layout: post
title: "[Eurekaproject] 블로그 생섭방법"
categories: eureka
tags: github eureka
comments: true
---
## Make GitHub blog

This `Readme.md` file describe how to build my git blog and it show the result      

**필수 과제(80%)**
* Remote Repository의 README.md 자신의 프로젝트를 Build한 과정을 기술 (50%) (✔)
* 특강에 다뤄졌던 내용 Topic 중 배운 내용에 관해 Post 작성 (10%) (✔)
* <username>.github.io를 접속했을 때 블로그가 정상적으로 동작 (10%) (✔)
* 기본 테마 이외의 목적에 맞는 테마 적용 (10%) (✔)

  
  
**선택 과제(20%)**
* Post에 댓글 기능 추가 (10%) (✔)
* 수업시간에 다루어지지 않은 기능(Google Analytics, Jekyll-admin 등)을 추가하고 이를 추가하는 과정을 Post로 작성(5%)(✔)
* 사이트에 favicon 추가 (5%) (✔)
----

## Build Git blog

Remote Repository를 자신의 이름으로 생성해줍니다.   
  
![image](https://user-images.githubusercontent.com/65720894/144546609-2bfe4a0a-380b-4f75-89e7-9f09ffa4ff2b.png)

블로그 관리를 로컬하게 관리하기 위해 다음과 같은 명령어로 clone을 시작합니다. 
  
`git clone https://github.com/LEFT-BEE/LEFT-BEE.github.io.git`
 
이후 블로그에 jekyll을 통해 테마를 적용합니다. [Claen blog](http://jekyllthemes.org/themes/clean-blog/)   
  
테마는 Clean Blog 테마를 골랐는데 그 이유로는 디자인이 깔끔하고 폰트가 간결하여 제가 작성한 포스트를 읽기 쉽게 되어있습니다   
또한 커스터마이징이 편리하여 블로그를 관리하는데 있어 이점이 있습니다. 
  
과정은 다음과 같습니다. 또한 이 모든 과정은 visual studio code의 터미널에서 이루어집니다.
  
![image](https://user-images.githubusercontent.com/65720894/144549425-997cf236-3a14-4ff0-a6d6-db75fc89840a.png)

ruby에서 지원하는 gem명령어를 통해 jekyll과 gem을 설치 해줍니다 gem이란 루비에서 제공하는 라이브러리를 손쉽게   
관리할 수 있게 해주는 도구입니다.   
  
`gem install jekyll bundler`
  
`jekyll new theorydb.github.io`
  
`bundle install`

구축된 블로그를 로컬환경에서 실행하려면 다음 코드를 실행한다.
  
`jekyll new theorydb.github.io`
  
### git blog file
  
이후 다양한 파일과 폴더들이 생성되는 데 각각 이렇게 정의된다.

```
LEFT-BEE.github.io
    - _featured_tags/ : 카테고리 대분류 폴더
    - _featured_categories/ : 카테고리 소분류(태그) 폴더
    - _data/ : 개발자 및 운영자, 기타 정보 폴더 (author.yml 수정이 필요)
    - _layouts/ : 포스트를 감싸기 위한 레이아웃 정의 폴더(페이지, 구성요소 등 UI변경 시 수정)
    - _includes/ : 재사용을 위한 기본 페이지 폴더
    - _posts/ : 포스트를 저장하는 폴더
    - .git/ : GitHub 연동을 위한 상태정보가 담긴 폴더
    - assets/ : 이미지, CSS 등을 저장 폴더
    - _site/ : Jekyll 빌드 생성 결과물 폴더
    - .sass-cache/ : 레일 엔진에서 사용하는 캐시 저장폴더
    - _sass/ : 일종의 CSS 조각파일 저장 폴더
    - _js/ : JavaScript 저장 폴더
- _config.yml : 가장 중요한 환경변수 설정 파일
- README.md : eureka project description
- favicon.ico : 블로그 상단에 위치한 아이콘
- about.md : 글쓴이를 소개하는 글
- Gemfile : 필요한 레일 기반 라이브러리를 자동으로 설치하고 싶을 때 명시하는 설정 파일
- .gitignore : git에 push하고 싶지 않은 파일들을 경로지정가능하다. 
- sitemap.xml : 테마의 사이트맵
- search.html : Tipue Search 설치 시, 검색결과를 출력하는 페이지로 활용
- robots.xml : 구글 웹로봇 등 검색엔진 수집 등에 대한 정책을 명시하는 설정파일
- LICENSE.md : 테마 개발자의 라이센스 설명
- index.html : 블로그 접속 시 보게되는 페이지
- 404.md : 404 Not Found Page
- .eslintrc : EcmaScript Lint 관련 환경 설정 파일
- .babelrc : Babel(Javacss compiler) 설정파일
 ```
  
### 파일을 local repo에 올리기
  
수정한 파일을 GitHub 저장소에 올리기 위해 다음과 같은 과정을 거친다.

Git Bash를 실행한 후, 아래 코드와 같이 모든 파일을 local repository에 올린다.
  
`git add *
  
수정된 파일들을 local repo에 commit해주고 push해준다.
  
`git commit -m "git blog"`
  
`git push`
  
이러한 방식을 걸쳐 블로그를 수정하고 이를 원격 저장소에 남길 수 있다.
  
---
  
## 블로그 소개 
  
`_data` 폴더 안에 `authors.yml`파일은 블로그의 저자를 수정할 수 있다.    

name과 email에 자기 자신의 정보를 입력하고 about에 소개를 적는다.   
src , 1x , 2x에는 나의 프로필 사진을 저장해주면 된다.
 
![image](https://user-images.githubusercontent.com/65720894/144588955-93917bfe-5641-4677-9ef2-a2d46ca48b7e.png)
![image](https://user-images.githubusercontent.com/65720894/144589768-a774e5a4-2f16-402d-a907-05f45e85c4fc.png)

그 외 `description`에 자가자신의 소개와 프로필 이미지를 설정해준다.
  
---
## 카테고리
  
카테고리는 `featured_categories` , `featured_tags` 폴더를 이용해 만들어준다. 딥러닝에 관심이 많으므로 `DeepLearning` 이라는 대 카테고리에서   
딥러닝 기술요소를 설명하는 `tech` , 논문 리뷰를 위해 'paper' , 코드분석을 위해 `code`와 같이 소 카테고리를 만들어 주었다. 
  
그 외 `math` , `others`와 같은 카테고리를 만들어 포스트의 카테고리에 맞게 정리할 수 있게 만들었다.    
**여기서 'Eureka Project'는 이번 깃 블로그를 만드는 과정을 서술 및 과제내용을 수행하기 위해만든 임시 카테고리이다**

`DeepLearning.md` 의 내용은 아래와 같다.
  
```
layout: list
title: DeepLearning
slug: deeplearning
menu: true
submenu: true
order: 2
description: >
  딥러닝 및 머신러닝에 필요한 지식 정리
```
 
---
  
## 사이드 바 
  
사이드 바는 `sidebar.html`의 파일에서 정의 되며 위의 요소들을 입력하면 아래와 같이 적용된다.

![image](https://user-images.githubusercontent.com/65720894/144591831-238b48f5-3cf7-4df4-9f3e-f2d2f6c14fe8.png)

---
## 포스트 
 
포스트는 `_posts` 폴더에 저장된다. 포스트내에서 title과 Category, tag, comments 등을 아래의 예시 같이 정의해준다.   
포스팅은 마크다운 형식으로 작성해주면 된다.

```
---
layout: post
title: "[Markdown2] How to make github blog"
subtitle: "Markdown 사용법2"
categories: others eureka
tags: github eureka
comments: true
---
```
![image](https://user-images.githubusercontent.com/65720894/144593362-109d28f6-2530-4df4-aa17-38df2f312998.png)

---
## 댓글 기능 

`Disqus` 를 사용해서 블로그에 댓글 기능을 추가시켰다.   

이후 `config.yml`에서 다음과 같이 disqus에서 설정해주었던 shortname과 `disqus : True`라 설정 해준다.
  
![image](https://user-images.githubusercontent.com/65720894/144594853-be55b273-0ba1-4756-ae53-2f43f630751e.png)

comments의 UI는 다음과 같이 `comments.html`에 정의되어있다.  
 
위 과정을 모두 수행하고 post를 할때 comments : true 라고 설정해주면 아래와 같이 댓글창이 생긴다.

![image](https://user-images.githubusercontent.com/65720894/144595548-63de51d3-bda6-431a-820d-20ee749b7f87.png)

---
## favicon 설정

favicon은 웹 브라우저 주소창에 표시되는 대표아이콘인데 본인은 codegongbang의 c를 의미하는 이미지를 사용하였다.
  
![image](https://user-images.githubusercontent.com/65720894/144595861-dc445edd-372d-4ad1-86b2-09f2061c9b91.png)

위 이미지는 favicon을 생성해주는 [favicon site](https://www.favicon-generator.org/) 에서 생성해 주었다.    
그러면 favicon.ico라는 파일이름으로 디렉토리에 넣어주게되면 아래의 코드를 통해 적용된다.

![image](https://user-images.githubusercontent.com/65720894/144596229-410c289a-0d90-428c-9dc9-8c62dffd6b53.png)

실제로 다음과 같이 적용된 것을 볼 수 있다.

![image](https://user-images.githubusercontent.com/65720894/144596284-7c31e206-3510-48e8-891b-c88f47add6a6.png)

---

## google analytics

google analytics에 가입하여 블로그에 오는 사용자들의 데이터를 분석한다.
 
![image](https://user-images.githubusercontent.com/65720894/144599406-cf50d0d4-7190-42d4-8be8-6be62f23d64b.png)

![image](https://user-images.githubusercontent.com/65720894/144599682-36dcad18-a0b9-4bbd-8e8b-64a6dcc9c32a.png)

![image](https://user-images.githubusercontent.com/65720894/144599741-f6c3048a-37e8-49e8-b2e8-3c9f7e7c2a5e.png)

위의 과정을 모두 수행하면 측정 ID를 얻게 되는데 이를 코드에서 수정해주어야 한다.

![image](https://user-images.githubusercontent.com/65720894/144599901-5dc76d94-dca1-4026-8ea0-ececa9f9fad6.png)
  
google analytics를 통해 블로그의 방문자 통계와 같은 정보를 얻을 수 있다.
  
![image](https://user-images.githubusercontent.com/65720894/145908521-dde0dae4-63bf-41ec-961c-0616e1096417.png)

![image](https://user-images.githubusercontent.com/65720894/145908552-a53ab637-6521-41b9-bd02-41a201abf47e.png)

---
  
## Tipue Search

`Tipue Search` 는 Jqurey를 활용해서 만들어진 site search plugi이다. 

해당 사이트에서 jekyll-tipue-search-master.zip 파일을 다운받아 압축을 푼다.   
[Tipue Github Repo](https://github.com/jekylltools/jekyll-tipue-search)

압축을 풀면 나오는 `search.html` 파일을 깃 블로그 최상위 디렉토리에 넣어주고   
`assets/`안에 있는 `tipuesearch` 폴더를 깃 블로그의 'assets'에 넣어준다.

`tipuesearch`의 내용은 다음과 같다.

![image](https://user-images.githubusercontent.com/65720894/144602866-514ced07-4092-435b-b67c-83300c2ea4a6.png)

이후 `config.yml`파일에 다음과 같은 코드를 추가해준다.

![image](https://user-images.githubusercontent.com/65720894/144603092-69c6269a-acc3-4d3c-95ab-5f2377e51b92.png)

include의 `pages : false`의 설정은 pages 레이아웃에 해당하는 html페이지는 검색하지 않겠다는 것을 의미한다.   
exclude의 `search.html , index.html , tags.html` 페이지는 검색에서 제외하겠다는 것을 의미한다.

'head.html' 파일을 열어 `LINKS`영역 바로 위의 위치에 다음의 코드를 추가한다.

![image](https://user-images.githubusercontent.com/65720894/144603556-70cb1d3e-2cd2-44ab-9fce-c5db9232f87e.png)

그 다음 `search.html`에 파일을 열어 아래와 같이 코드를 다음과 같이 설정해준다.

![image](https://user-images.githubusercontent.com/65720894/144604088-10a2e381-6254-4fc2-a124-c70c2c2d95bf.png)

위의 설정 내용은 위의 `tipue` 깃에 나와있다.

마지막으로 `sidebar.html` 에 아래와 같이 설정한다. 본인은 검색창을 사이드 바에 위치하게 해서 `sidebar.html`에 코드를 추가해주었다.

![image](https://user-images.githubusercontent.com/65720894/144605978-fa0c56ee-2de9-412e-a0ec-e45058549981.png)

아래와 같이 잘 실행되는 것을 볼 수 있다. 아래 예시는 검색창에 GAN을 검색했을 때 나오는 페이지이다.

![image](https://user-images.githubusercontent.com/65720894/144606339-a1a9a3c4-1f27-4fca-bcc6-5fe13299cc35.png)






  
