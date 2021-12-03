## Make GitHub blog

This `Readme.md` file describe how to build my git blog and it show the result      

**필수 과제(80%)**
* Remote Repository의 README.md 자신의 프로젝트를 Build한 과정을 기술 (50%) (✔)
* 특강에 다뤄졌던 내용 Topic 중 배운 내용에 관해 Post 작성 (10%) (✔)
* <username>.github.io를 접속했을 때 블로그가 정상적으로 동작 (10%) (✔)
* 기본 테마 이외의 목적에 맞는 테마 적용 (10%) (✔)

  
  
**선택 과제(20%)**
* Post에 댓글 기능 추가 (10%) (✔)
* 수업시간에 다루어지지 않은 기능(Google Analytics, Jekyll-admin 등)을 추가하고 이를 추가하는 과정을 Post로 작성(5%)( )
* 사이트에 favicon 추가 (5%) (✔)
----

### Build Git bl

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
  
수정한 파일을 GitHub 저장소에 올리기 위해 다음과 같은 과정을 거친다.

Git Bash를 실행한 후, 아래 코드와 같이 모든 파일을 local repository에 올린다.
  
`git add *
  
수정된 파일들을 local repo에 commit해주고 push해준다.
  
`git commit -m "git blog"
  
`git push`

  
