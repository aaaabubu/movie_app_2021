# 안진홍 201840121

## [ 11월 17일 학습내용 ]
>오늘 배운 내용 정리

### 상태를 가지는 컴포넌트
- 컴포넌트는 this.props를 이용해 입력 데이터를 다루는 것 외에도 내부적인 상태 데이터를 가질 수 있습니다. 이는 this.state로 접근할 수 있습니다. 컴포넌트의 상태 데이터가 바뀌면 render()가 다시 호출되어 마크업이 갱신됩니다.

      class Timer extends React.Component {
      constructor(props) {
      super(props);
      this.state = { seconds: 0 };
      }

      tick() {
      this.setState(state => ({
      seconds: state.seconds + 1
      }));
      }

      componentDidMount() {
      this.interval = setInterval(() => this.tick(), 1000);
      }

      componentWillUnmount() {
      clearInterval(this.interval);
      }

      render() {
      return (
      <div>
        Seconds: {this.state.seconds}
      </div>
      );
      }
      }

      ReactDOM.render(
      <Timer />,
      document.getElementById('timer-example')
      ;

### 애플리케이션
- props와 state를 사용해서 간단한 Todo 애플리케이션을 만들 수 있습니다. 이 예제에서는 state를 사용해 사용자가 입력한 텍스트와 할 일 목록을 관리합니다. 이벤트 핸들러들이 인라인으로 각각 존재하는 것처럼 보이지만, 실제로는 이벤트 위임을 통해 하나로 구현됩니다.

      class TodoApp extends React.Component {
      constructor(props) {
      super(props);
      this.state = { items: [], text: '' };
      this.handleChange = this.handleChange.bind(this);
      this.handleSubmit = this.handleSubmit.bind(this);
      }

      render() {
      return (
            <div>
            <h3>TODO</h3>
            <TodoList items={this.state.items} />
            <form onSubmit={this.handleSubmit}>
            <label htmlFor="new-todo">
                  What needs to be done?
            </label>
            <input
                  id="new-todo"
                  onChange={this.handleChange}
                  value={this.state.text}
            />
            <button>
                  Add #{this.state.items.length + 1}
            </button>
            </form>
            </div>
      );
      }

      handleChange(e) {
      this.setState({ text: e.target.value });
      }

      handleSubmit(e) {
      e.preventDefault();
      if (this.state.text.length === 0) {
            return;
      }
      const newItem = {
            text: this.state.text,
            id: Date.now()
      };
      this.setState(state => ({
            items: state.items.concat(newItem),
            text: ''
      }));
      }
      }

      class TodoList extends React.Component {
      render() {
      return (
            <ul>
            {this.props.items.map(item => (
            <li key={item.id}>{item.text}</li>
            ))}
            </ul>
      );
      }
      }

      ReactDOM.render(
      <TodoApp />,
      document.getElementById('todos-example')
      );

### 외부 플러그인을 사용하는 컴포넌트
- React는 유연하며 다른 라이브러리나 프레임워크를 함께 활용할 수 있습니다. 이 예제에서는 외부 마크다운 라이브러리인 remarkable을 사용해 textarea의 값을 실시간으로 변환합니다.

      class MarkdownEditor extends React.Component {
      constructor(props) {
      super(props);
      this.md = new Remarkable();
      this.handleChange = this.handleChange.bind(this);
      this.state = { value: 'Hello, **world**!' };
      }

      handleChange(e) {
      this.setState({ value: e.target.value });
      }

      getRawMarkup() {
      return { __html: this.md.render(this.state.value) };
      }

      render() {
      return (
            <div className="MarkdownEditor">
            <h3>Input</h3>
            <label htmlFor="markdown-content">
            Enter some markdown
            </label>
            <textarea
            id="markdown-content"
            onChange={this.handleChange}
            defaultValue={this.state.value}
            />
            <h3>Output</h3>
            <div
            className="content"
            dangerouslySetInnerHTML={this.getRawMarkup()}
            />
            </div>
      );
      }
      }

      ReactDOM.render(
      <MarkdownEditor />,
      document.getElementById('markdown-example')
      );
      

## [ 11월 10일 학습내용 ]
>오늘 배운 내용 정리

### 컴포넌트 설치 오류

      - npm WARN read-shrinkwrap This version of npm is compatible with lockfileVersion@1, butpackage-lock.json was generated for lockfileVersion@2. I'll try to do my best with it!
      - npm ERR! Maximum call stack size exceeded
      - npm ERR! A complete log of this run can be found in:
      - npm ERR! C:\Users\82102\AppData\Roaming\npm-cache\_logs\2021-10-27T07_07_17_243Z-debug.log

### package.json과 package-lock.json 차이

      • package.json은 패기지 의존성 관리 파일이다.
      • 협업을 할 때는 팀원들 각자의 컴퓨터에 같은 패키지들을 설치해서 동일한 개발환경을 구성해야 하는게 이때 사용하는 것이 package.json이다.
      • 물론 개인의 프로젝트를 재 생성하거나, 이번의 경우처럼 오류가 있을 때도 유용하게 사용된다.
      • 하지만 간혹 팀원들 중 버전이 다르게 설치되는 경우 앱이 동작하지 않는 경우도 있어 난감할 때가 생긴다. 이때는 다음과 같이 확인하고 조치한다. 결국 앞서 소개한 방법으로 해결하는 것이다.
      • package.json의 경우는 version range를 사용한다. "express": "~4.16.1“
      • package-lock.json은 package.json 이 변경될 때 마다 업데이트 되는 것으로 좀더 정확한 버전이기록되어 있다.
<b>그러나...<b>

      • npm install 을 진행하더라도 서로 다른 node_modules트리를 생성하는 경우가 발생한다.
      • 이것은 npm의 버전이 다른 경우, npm 알고리즘의 차이가 나기 때문이다. 
      • 그래서 팀원 간의 문제가 발행하면 npm버전부터 확인한다.
      • 그리고 package-lock.json파일이 있으면 npm install명령은 package.json 을 사용하지 않고package-lock.json 을 사용하여 node-modules 를 생성한다.




### 컴포넌트 설치 오류 등 원인 규명이 되지 안은 오류가 있을 경우

      $ npm cache clean --force 
      $ npm rebuild
      $ rm -rf node_modules
      $ npm install

      * 만일 rm명령이 실행되지 않으면 shell을 관리자 권한으로 실행한 후 다시 시도해 본다.
      * 그래도 안될 경우는 탐색기에서 삭제하면 되는데 시간이 조금 걸릴 수는 있다.
      * 원인 모를 문제가 발생했을 때 cache clean과 rebuild를 통해 많은 부분 해결되기도 한다.
## [ 11월 3일 학습내용 ]
>오늘 배운 내용 정리
##  영화 상세 정보 기능 만들어 보기

      • route props를 이용해 영화 카드를 누르면 상세 정보를 보여주는 기능을 만들어 본다.
      • route props는 라우팅 대상이 되는 컴포넌트에 넘겨주는 기본 props를 말한다.

### route props 살펴보기

      • 먼저 consol.log()를 통해 About으로 어떤 props가 넘어오는지 확인한다.
      • react-router-dom에서 Route 컴포넌트가 그려줄 컴포넌트에 전달한 props를 확인할 수 있다.
      • Route 컴포넌트가 그려줄 컴포넌트에는 항상 이 props가 전달되며, 이 props는 원하는 데이터를 담아 보낼 수 있다.

### route props에 데이터 담아 보내기

      • 데이터를 담아 보내려면 Navigation 컴포넌트에 있는 Link컴포넌트의 to props의 구조를 조금 바꿔야 한다.
      • pathname은 URL을 의미하며, state는 우리가 route props에 보내줄 데이터를 의미한다.

###  route props 다시 살펴 보기

      • /about으로 이동한 후 console에서 location을 살펴본다.
      • state 키에 우리가 보낸 데이터를 확인할 수 있다.

### Navigation 컴포넌트 정리하기

      • 액션03까지 작성한 코드를 원래대로 돌려 놓는다.

### Detail 컴포넌트 만들기

      • Detail 컴포넌트를 routes폴더에 추가한다.
      • Detail컴포넌트에서 Movie컴포넌트의 Link 컴포넌트가 보내준 영화 데이터를 확인할 수 있도록console.log()를 작성한다.
      • 아직은 console.log(props)는 확인할 수 없다. 액션07로 App.js에서 Route컴포넌트를 추가하면 확인할 수 있다.

### Movie 컴포넌트에 Link 컴포넌트 추가하기

      • Movie컴포넌트를 클릭하면 영화 상세 정보 페이지로 이동해야 함으로 Movie컴포넌트에 Link컴포넌트를 추가한다.
      • Movie컴포넌트에 Link컴포넌트를 import하고, Link컴포넌트에 to props를 작성한다.
      • 이때 Link컴포넌트의 위치에 주의한다.
      • 이제 영화 카드를 누르면 /movie-detail로 이동하게 된다.

### Route 컴포넌트 추가하기

      • App.js에 Detail 컴포넌트를 import하고 Route 컴포넌트에서 Detail컴포넌트를 그려 주도록 코드를 작성한다.

### 영화 카드를 눌러 /movie-detail로 이동한 다음 영화 데이터 확인하기

      • 영화 카드를 클릭해서 /movie-detail로 이동한다.
      • 화면에는 Detail 컴포넌트가 출력하는 hello라는 문장을 확인할 수 있다.
      • console에는 location -> state에 Movie컴포넌트에서 Link 컴포넌트를 통해 보내준 데이터가 들어있는지 확인해 본다.

### /movie-detail로 바로 이동하기

      • URL에 /movie-detail을 입력해서 바로 이동해 보자.
      • 그리고 console창을 확인한다.
      • Detail 컴포넌트의 hello는 잘 출력하고 있지만 console에는 영화데이터가 보이지 않는다. Detail컴포넌트로 영화 데이터가 넘어 오지 못한 것이다.
      • 이런 경우에는 사용자를 강제로 Home으로 돌려 보내야 한다.

### 리다이렉트 기능 만들어 보기

      • 리다이렉트 기능을 사용하기 위해서는 route props의 history 키를 활용해야 한다.
      • history키에는 push, go, goBack, goForward와 같은 키가 있으며, 그 키에는 URL을 변경해 주는함수들이 있다. 
      • 이 함수들을 이용해서 리다이렉트 기능을 구현한다.

### History 키 살펴보기

      • 주소창에 localhost:3000을 입력해서 이동한 다음 아무 영화나 눌러 이동한다.
      • console에서 history에 출력된 값을 확인한다.
      • 액션03에서는 componentDidMount() 생명주기 함수를 사용해 Detail 컴포넌트가 마운트될 때push()함수를 실행하도록 한다.

### Detail 컴포넌트 클래스형 컴포넌트로 변경하기

      • Detail 컴포넌트를 함수형에서 클래스형으로 변경한 후 location, history키를 구조 분해 할당한다.

### push() 함수 사용하기

      • location.state가 undefined인 경우 history.push(“/”)를 실행하도록 코드를 작성한다.

## [ 10월 27일 학습내용 ]
>오늘 배운 내용 정리

## react-router-dom 설치하고 프로젝트 폴더 정리하기

      • 간단한 메뉴를 추가한다.
      • 메뉴를 클릭하면 화면이 이동해야 하는데, 이때 필요한 것이 라우터이다.
      • 라우터는 react-router-dom패키지를 이용하면 된다.
###  react-router-dom 설치하기

      > npm install react-router-dom

### components 폴더에 Movie 컴포넌트 옮기기

      • src/components 폴더 만들고 Movie컴포넌트 이동

### routes 폴더에 라우터가 보여줄 화면 만들기

      • src/routes 폴더 만들고 Home.js와 About.js 파일 생성

### Home.js 수정하기

      • App.js내용을 Home.js로 복사하고 컴포넌트 이름을 Home으로 수정한다.
      • Home.css를 생성하고 Home.js에 import한다.

### Home.css 만들기 / 액션06. App.js 수정하기

      • Home.css의 내용은 교재 소스 그대로 사용한다.
      https://github.com/easysIT/do_it_clonecoding_movieapp

## 라우터 만들어 보기

      • 라우터는 사용자가 입력한 URL을 통해 특정 컴포넌트를 불러준다.
      • 예) loclahost:3000/about
      • React-router-dom은 여러 종류의 라우터를 제공하는데, 여기서는 HashRouter와 Route컴포넌트를 사용한다.
      • App.js에 HashRouter와 Route 컴포넌트 import하고 적용한다.

###  HashRouter와 Router컴포넌트

      • HashRouter와 Router컴포넌트 import한다.
      • HashRouter컴포넌트가 Route컴포넌트를 감싸 반환하도록 App.js를 수정한다.
      • 그리고 Home을 import 했던 코드는 잠시 지운다.
      • Route에는 2가지 props를 전달할 수 있다. 하나는 URL을 위한 path props고, 또 하나는 URL에 맞는 컴포넌트를 불러 주기 위한 component props이다.

### Route 컴포넌트에 path, component props 추가하기

      • About 컴포넌트를 import 한다.
      • Path, component props에 URL과 About 컴포넌트를 전달한다.

### About.js 수정하기

      • About.js의 내용이 비어 있으니 간단하게 채우자.
      
      import React from "react";
      function About(){
            return <span>About this page: I built it because I Love movies.</span>
            }
            export default About;

### 라우터 테스트해 보기

      • localhost:3000/#에 path props로 전달했던 값 /about을 붙여 다시 접속해 보자.
      • URL은 localhost:3000/#/about이고, 액션3에서 작성했던 내용이 출력된다.
      • 이것은 Route 컴포넌트에 전달한 path props를 보고 component props에 지정한 About 컴포넌트를 그려 준 것이다. 

### Home 컴포넌트를 위한 Route컴포넌트 추가하기

      • App 컴포넌트에 Home 컴포넌트를 import하고, 또 다른 Route 컴포넌트를 추가 한다.
      • path props를 “/”으로 입력한 이유는 localhost:3000에 접속하면 기본으로 보여줄 컴포넌트를Home 컴포넌트로 하기 위해서이다.

### 라우터 테스트하고 문제 찾아보기

     • localhost:3000에 접속하면 주소 뒤에 자동으로 /#/가 붙으면서 영화 앱 화면이 나타난다.
     • 이번에는 /about에도 접속해 본다. 당연히 About 컴포넌트만 출력되야 함에도, Movie 컴포넌트가함께 출력된다.

### 라우터 자세히 살펴보기

### 라우터 다시 테스트해 보기

      • /home에 접속하면 문제가 없어 보이지만, /home/introduction에 접속하면 Home이 함께 나오는문제는 여전하다.
      • 라우터는 사용자가 /home/introduction에 접속하면 /, /home, /home/introduction 순서로path props가 있는지를 찾는다. 그런데 이들 모두가 path props를 갖고 있기 때문에 introduction에 접속하면 이 모든 것이 보이는 것이다.
      • 이와 같은 이유로 /about에 접속하면 /, /about 순서로 path props를 찾기 때문에 Home과 About컴포넌트 모두 출력되는 것이었다.

### App 다시 원래대로 돌리기

      • 이제 App.js를 액션08에서 테스트 했던 이전 상태로 되돌리자.
      • 액션08에서 확인한 문제는 어떻게 하면 고칠 수 있을까?
            - Route 컴포넌트에 exact props를 추가하면 해결할 수 있다.
            - exact props는 Route 컴포넌트가 path props와 정확하게 일치하는 URL에만 반응하도록 한다.

### About.css 작성하기

      • route 폴더에 About.css파일을 생성한고, css 코드를 작성한다. (kabab case사용하기)
      • About.js에 About.css를 import하고, 적용할 수 있도록 내용도 수정한다.

## 네비게이션 만들어 보기

### Navigation 컴포넌트 만들기

      • Home과 About이라는 2개의 버튼을 만들고, 각각의 버튼을 눌렸을 때 해당 화면이 보이도록 할 것이다.
      • 먼저 components폴더에 Navigation.js파일을 만들고, 2개의 a 태그를 반환하도록 작성한다.

### Navigation 컴포넌트 App 컴포넌트에 포함 시키기

      • App컴포넌트에 Navigation을 import시키고, <HashRoute>에서 불러오게 한다.
      • 앱을 실행하면 Navigation이 출력되는 것을 확인할 수 있다.
      • 하지만 동작은 잘 될까? 다음 액션에서 확인해 보자.

### Home 링크 눌러 보기

      • Home링크를 눌러본다.
      • 링크를 클릭할 때마다 리액트가 죽고, 새 페이지가 열리는 문제가 발생한다.
      • 원인은 a 태그의 href속성이 페이지 전체를 다시 그리는 성질을 갖고 있기 때문이다.
      • 이대로 라면 필요한 부분만 다시 그려주는 리액트를 써야할 이유가 없다.
      • 이 문제를 해결하려면 react-router-dom의 Link 컴포넌트를 사용하면 된다.

### a 태그 Link 컴포넌트로 바꾸기

      • Navigation 컴포넌트에 Link 컴포넌트를 import하고 a 태그를 Link 컴포넌트로 바꾼다.
      • href속성은 to로 수정한다.
      • 앱을 실행하고 링크를 클릭해 본다. 페이지 전체 고침 문제가 해결된 것을 확인할 수 있다.

### Navigation 컴포넌트 위치 다시 확인하기

      • Link, Router 컴포넌트는 반드시 HashRouter안에 포함되어야 한다
      • 따라서 HashRouter 바깥에 위치했던 Navigation 컴포넌트를 안쪽으로 이동해야 한다.

### Navigation 컴포넌트 스타일링하기

      • components 폴더에 Navigation.css 파일을 만들고, css코드를 작성한다.
      • Navigation 컴포넌트에 css를 import하고 이를 적용시킨다.

## [ 10월 13일 학습내용 ]
>오늘 배운 내용 정리

## Movie 컴포넌트 만들기

### Movie 컴포넌트 만들기

      • src폴더에 Movie.js 파일을 새로 만든다.
      • 컴포넌트의 기본 골격을 작성한다.
      • Movie 컴포넌트는 state가 필요하지 않으므로 클래스형 컴포넌트가 아닌, 함수형 컴포넌트로 작성하기로 한다.
      • Movie에 넘어와야 하는 영화 데이터를 정의하고, 관리하기 위해 prop-types를 사용한다. 

### 영화 데이터 다시 살펴보기

      • yts-proxy.now.sh/list_movies.json에 접속해서 사용할 영화 데이터를 다시 확인한다.
      • 데이터 중 필요한 것만 골라서 영화 앱에 반영한다.

### Movie.propTypes 작성하기

      • 먼저 id를 Movie.propTypes를 추가 한다.
      • id의 자료형은 Number이고, 반드시 있어야 함으로 PropType.number.isRequired로 작성한다.
      • year, title, summary, poster를 각각 Movie.propTypes에 추가 한다.
      • 여기서 poster props는 영화 포스터 이미지 주소를 저장하기 위한 것이다.

### 노마드 코더 영화 API 정렬 기능 사용해 보기

      • yts.lt/api#list_movies에 접속한 다음 Endpoint Parameters에 sort_by라는 Parameter가 있다. 우리는 이 Parameter를 사용한다.
      • sort_by Parameter를 사용하기 위해 Examples를 참고한다.
      • yts-proxy.now.sh/list_movies.json?sort_by=rating접속해 보자.
      • 평점을 기준으로 내림차순으로 영화 데이터를 정렬해 보여 주는 것을 확인할 수 있다.

### axios.get() 수정하기

      axios.get()에 yts-proxy.now.sh/list_movies.json?sort_by=rating을 전달한다

###  Movie 컴포넌트에서 props를 추가하고 출력해 보기

      • Movie 컴포넌트에서 id, title, year, summary, poster props를 받아 출력할 수 있도록 수정한다.
      • App 컴포넌트에서 Movie컴포넌트를 그릴 때 title만 우선 출력하도록 만들어 보자.
      • 음식 앱을 만들 때 컴포넌트를 map() 함수로 출력했던 것과 같이 코딩한다.

### App 컴포넌트에서 Movie컴포넌트 그리기

      • We are ready를 출력하고 있는 자리, 즉 로딩이 완료 되면 실행되는 자리에 movies.map()을 사용한다.
      • map() 함수의 첫 번째 인자로 컴포넌트를 반환하는 함수를 전달하면 된다.

### map() 함수에 컴포넌트를 반환하는 함수 전달하기

      • 우선 console탭에 영화 데이터를 출력한 다음, 아무것도 반환하지 않는 함수를 전달해 본다.

### Movie 컴포넌트를 반환하도록 movies.map() 수정하기

      • App.js에 Movie 컴포넌트를 import한 다음, movies.map()에 전달한 함수가 <Movie />를 반환하도록 한다.

### Movie컴포넌트에 props 전달하기

      • id, year, title, summary, poster를 isRequired로 설정했기 때문에 props를 모두 전달해야 한다.
      • 단, poster props의 경우 키 이름이 medium_cover_image이므로 movies.poster가 아니라movies.medium_cover_image라고 작성한다.
      • App을 실행해 보면 영화 제목이 나오는 것을 확인할 수 있다.

### console탭에서 영화 데이터 확인해 보기

      • 오류는 음식앱에서 다루었던 내용이다.
      • 다음 액션에서 처리하도록 한다.

### key props 추가하기

      • key props문제를 해결하자.
      • key props는 유일해야 함으로 API에서 넘겨주는 id값을 사용한다.
      • console.log()는 사용하지 않음으로 삭제한다.

## 영화 앱 스타일링 하기 - 기초

### App컴포넌트에 HTML추가하기

      • App컴포넌트가 반환할 JSX 바깥쪽을 <section class=“container”>로 감싼다.
      • Loading…은 <div class=“loader”><span class=“loader”>로 감싼다.
      • movies.map()은 <div class=“movies”>로 감싼다.

### Movie컴포넌트에 HTML추가하기

      • Movie 컴포넌트가 반환할 JSX를 <div class=“movie”>로 감싼다
      • 그 안에서 title, year, summary를 목적에 맞는 tag로 감싼다.

### 영화 포스터 이미지 추가하기

      • 전체 tag를 감싸는 div tag(class=“movie”)를 추가 한다.
      • img tag를 그 아래 추가해서 src속성에는 poster props를, alt속성에는 title props를 전달한다.

### Movie 컴포넌트 정리하기

      • Movie 컴포넌트에는 id props가 필요 없으니 제거해 준다.

### style속성으로 title 스타일링하기

      • h3 tag에 style 속성을 추가하고 backgroundColor를 red로 준다.

### CSS파일 생성

      • src폴더에 App.css, Movie.css를 생성한다.

### App, Movie 컴포넌트에 CSS 파일 import하기
### App.css파일 작성하기

      배경색을 어두운 색으로 바꿔본다.

## 영화 앱 전체 모습 수정하기

      • 페이지의 디자인은 다음과 같이 한다.
      • 왼쪽 위에는 살짝 튀어나온 듯한 느낌의 영화 포스트
      • 오른쪽에는 제목, 장르, 평점(별 표시), 시놉시스

###  App.css 내용 모두 지우기

      • 만일 삭제하거나 없다면 새로 생성한다.

### Movie 컴포넌트에 genres props 넘겨주기

      • 런타임(runtime) 아래 있는 장르(genres)를 추가해 준다

### Movie 컴포넌트 수정하기

      • App컴포넌트에서 Movie컴포넌트에 genres props를 넘겨준다.
      • console을 확인해 보면 두가지의 warning을 확인할 수 있다. 

### App 컴포넌트 수정하기

      • genres props가 Movie 컴포넌트에 undefined로 넘어 왔다는 부분 부터 수정한다.
      • 이는 App컴포넌트에서 해당 props를 Movie컴포넌트로 전달 하기만 하면 된다.
      • 메시지가 사라졌는지 console을 확인해 보자.

### class 속성 이름 className으로 바꿔 주기

      • 먼저 이런 메시지는 왜 나오는 것일까?
      • 이는 HTML의 class와 자바스크립트의 class라는 이름이 겹치면 리액트가 혼란스러울 수 있기 때문이다.
      • console에서 확인한다.
      • 이와 유사한 예를 하나 더 설명하면, 바로 label문의 for element이다.
      • 이것도 for=“name”이 아니라 htmlFor=“name”으로 작성한다.
      * https://www.w3schools.com/tags/tag_label.asp 참고

###  영화 장르 출력하기

      • Movie컴포넌트에서 장르를 출력하도록 코드를 수정한다.
      • genres props가 배열이므로 map()함수를 사용한다.
      • genres props를 ul, li 태그로 감싸서 출력한다.
      • console을 확인하면 kye props가 없다는 메시지가 나온다.

### li tag에 key props 추가하기

      • 그런데 genre에는 key값으로 사용하기에 적당한 id값 같은 것이 없다.
      • 이럴 경우 새롭게 만들에 내야 하는데, map()함수에는 2번째 매개변수를 지정할 경우 배열의 index값을 반환해 주는 기능이 있다.
      • 이것을 이용해서 배열의 인덱스를 key props로 활용하는 것이다.
      • console을 확인해 본다.

### App.css 수정하기

      • 사이트 전체에서 사용할 기본적인 글꼴, 배경 등을 설정한다.

### Movie.css 수정하기
### 시놉시스 180자로 제한하기

      • 자바스크립트의 slice함수를 이용하여 구현한다.

### 영화 앱 제목 살펴보기
### 영화 앱 제목 바꾸기

      • public폴더의 index.html에서 title을 수정한다.

## [ 10월 6일 학습내용 ]
>오늘 배운 내용 정리

## 영화 API사용해 보기
### axios 설치하기

      • javascript에서는 영화 데이터를 로딩 할 때 fetch()함수를 사용한다.
      • 하지만 이 시간은 javascript시간이 아님으로 그 대신 axios를 사용하도록 한다.
      • 터미널에서 다음과 같이 입력하여 axios를 설치한다.

### YTS영화 데이터 API 살펴보기

      • 브라우저 주소창에 yts.lt/api 라고 입력하고, YTS영화 데이터 API 사이트에 접속해 보자.
      • 앞으로 사용할 API는 'List Movies API'이다.
      • List Movies를 클릭한다. 로그인 하지 않아도 된다.
      • API는 특정 주소를 입력하면 그 주소에 맞는 결과를 보내 준다.
      • 조건도 붙일 수 있도록 제공한다.
      • 우리는 Endpoint의 가장 위에 있는 주소를 사용한다. 이 주소는 최신 영화 20개에 대한 데이터를 기본으로 보내 준다. https://yts.mx/api/v2/list_movies.json

 ### 영화 목록 데이터 확인해 보기

      • 브라우저에서 Endpoint의 주소 중 json으로 끝나는 주소를 입력한다.
      • min스타일로 제공되기 때문에 보기가 아주 불편하다.

### JSON Viewer 확장 도구 설치하기

      • JSON Viewer라는 확장 도구를 설치하면 정상적으로 볼 수 있다.
      • 크롬 웹스토어에서 JSON Viewer라고 검색하고 설치한다.

### 노마드 코더 영화 API를 사용하자

      • API GitHub에 접속해 보면 README.md 소개 글에 다음과 같은 내용이 있다.
      • YTS의 endpoint /list_movies.json을 사용하려면 yts-proxy.now.sh에 /list_movies.json을 붙이면 된다.
      • 만일 YTS의 다른 endpoint와 함께 노마드 코더 영화 API를 사용하려면,yts-proxy.now.sh에 endpoint를 붙이면 된다. 

### 영화 정보 더 자세히 살펴보기

      • 영화정보를 좀더 자세히 살펴보자.
      • 주소창에 yts-proxy.now.sh/movie_details.json라고 접속하면 아무 것도 출력되지 않는다.
      • API가 movie_id라는 조건을 요구하기 때문이다.

### 영화 정보를 더 자세히 보기 위해 조건 추가하기

      • yts.mx/api#mivie_details에 접속하면movie_details Endpoint는 movie_id가 필수임을 알수 있다.
      • 즉 yts-proxy.now.sh/list_movies.json에 movie_id를추가해야 한다.
      • Example에 있는 주소를 보면 movie_id를 어떻게 추가하는지 알 수 있다.
### movie_id가 10인 영화 정보 살펴보기

      • yts-proxy.now.sh/list_movies.json?movie_id=10 이하고 접속하면 아이디가 10인 영화의 자세한 정보를 볼 수 있다.

### 노마드 코더 영화 API를 영화 앱에서 호출하기

      • API를 사용하려면 axios를 import한 다음, componentDidMount()함수에서 axios로 API를 호출하면 된다.
      • axios.get()함수의 인자에 URL을 전달하여 API를 호출했다.
      • setTimeout은 이제 사용할 필요가 없으니 삭제한다.
      • 실행해 보면 여전히 Loading... 이라고만 나온다.

###  axios의 동작 확인해 보기

      • axios가 동작하는지 살펴보자.
      • network탭을 열고 브라우저 새로고침(Ctrl+F5)을 한다.
      • name이라는 항목에 list_movies.json이라고 나온 부분이 바로 axios가 API를 호출해서 발생한 것이다.

###  getMovies()함수 기다린 다음, axios.get() 함수가 반환한 데이터 잡기

      • getMovies()함수를 만들고, 이 함수 안에서 axios.get()이 실행하도록 한다.
      • axios.get()의 return값은 movies에 저장한다.
      • componentDidMount()함수가 실행되면 this.getMovie()가 실행된다. 
      • 이때 자바스크립트에게 getMovies()함수는 시간이 필요하다는 것을 알려야 하는데 이때 사용되는것이 async, await 이다.

###  getMovies()에 async 붙이고, axios.get()에 await붙이기

      • 시간이 필요하다는 것을 알리기 위해서는 async, await 키워드가 필요하다.
      • 자바스크립트에게 시간이 필요하다는 것을 알리려면 async를 ()앞에 붙이고,
      • 실제 시간이 필요한 대상인 axios.get()함수 에는 await을 붙인다.

## 영화 데이터 화면에 그리기
###  console.log() 함수로 영화 데이터 출력해 보기

      • 앞에서 우리가 작업한 결과로 API가 보내준 데이터는 movies에 들어가 있을 것이다.
      • console을 통해 출력해 보자.

### 영화 데이터 자세히 살펴보기

      • 출력된 데이터를 세심히 살펴 어떻게 사용할 지를 구상한다.
      • 특히 data키에 집중하여 살펴본다.

###  객체에 있는 movies키에 접근하기

      • movies변수에 있는 movies 키의 값을 추출해 보자.
      • movies.data.data.movies와 같이 수정한 후 콘솔에서 확인해 본다.
      • 이제 우리가 원하는 데이터만 추출된 것을 확인할 수 있다.
      • 이 데이터 구조는 음식 앱을 만들 때 사용했던 데이터 구조와 동일하다.

### 객체에 있는 movies 키에 조금 더 똑똑하게 접근하기

      • movies.data.data.movies와 같은 방법으로 객체에 접근하는 방법 말고는 없을까?
      • ES6를 사용한다면 구조 분해 할당이란 방법을 사용해 보자.

### movies state에 영화 데이터 저장하기

      • this.setState({ movies: movies })와 같이 작성해서 movies state에 영화 데이터를 저장한다.
      • 이제 consol을 통해 확인하지 않아도 되니 이 부분은 삭제 한다.
      • ES6에서는 객체의 키와 대입할 변수의 이름이 같다면 코드를 축약할 수 있다.
      • this.setState({ movies: movies })를 this.setState({ movies })로 수정한다.

### isLoading state를 true에서 false로 업데이트하기

      • 아직 앱을 실행하면 여전히 Loading만 출력된다.
      • 여기서 “영화 데이터의 출력”를 출력하려면 isLoading state의 값을 true에서 false로 업데이트하면 된다.
      • 앱이 실행되면 처음에는 Loading...이 화면에 나타나다가 조금 시간이 지나면 We are ready로 변하는 것을 확인할 수 있다.
      • 이제 “영화 데이터의 출력” 대신 movies state를 출력하면 된다.





## [ 9월 29일 학습내용 ]
>오늘 배운 내용 정리

### 상대경로 이미지 삽입 방법

      교재에서의 이미지 삽입은 절대 경로를 사용했다. 상대 경로로 삽입할 때는 어떻게 하면 될까?
      Import를 사용하는 방법도 있지만 이미지가 많을 경우는 코드가 길어져 적합하지 않다.
      public 폴더에 images폴더를 생성한다.
      그리고 필요한 곳에 <img src=“image/[이미지 이름]”> 형태로 태그를 작성하면 된다.

##### 음식 앱에 prop-types 도입하기

      정의한 props의 값이 컴포넌트에 제대로 전달되지 않으면 어떻게 해야 할까?
      Picture props에 {dish.image}가 아닌 {true}를 전달하면 어떻게 될까?
      여러가지 이유로 원하는 대로 이미지 등이 나오지 않을 때 이를 검사할 수 있는 방법은 없을까?
      이번 절에서는 이 검사 방법에 대해 알아 보도록 한다.

- 음식 데이터에 rating 추가하기

      foodLike 배열의 각 요소에 rating(평점)을 추가한다.
      값의 자료형은 number로 한다.
      Rating props를 Food 컴포넌트에 전달하면서 이 값을 검사해 보기로 한다.
      그러기 위해서는 props의 자료형을 검사할 수 있도록 만들어 주는 prop-types라는 도구가 필요하다.
      다음 액션에서는 도구를 설치해 본다.

- prop-type 설치하기

      터미널에서 다음 명령을 입력해서 prop-types를 설치한다
      > npm install prop-types

- 정상 설치 여부 확인

      Package.json 파일을 열어 dependencies 키에 있는 값을 살펴보자.
      Prop-types가 등록되어 있으면 설치가 정상적으로 된 것이다.
      Prop-types는 컴포넌트가 전달받은 props가 원하는 값인지 확인해 주는 역할을 한다.
      prop-types를 통해 미리 ‘Food 컴포넌트는 반드시 picture props가 전달되야 한다’고 정의할 수 있다.

- prop-types 적용하기

      import PropTypes from ‘prop-types’; 를 App.js 파일 맨 위에 추가해 주자.
      그리고 ration props를 Food 컴포넌트에 전달하자.
      아직 prop-types를 적용한 상태는 아니다.

- Food.propTypes 작성하기

      이제 prop-types를 적용해 보자.
      Food.propType에 객체를 적어 주기만 하면 된다.
      모든 props는 문자열이고 반드시 있어야 한다는 조건을 추가해 보자.
      실행하면 별 문제가 없어 보이지만, 콘솔 탭을 확인해 보면 경고 메시지가 보인다.

- Food.propTypes의 rating 키 값 확인하기

      isRequired는 필요하다는 뜻 이다.
      ‘rating에는 string이라는 자료형이 반드시 필요하다’는 이야기 이다.

- prop-types 경고 해결하기

      rating: PropTypes.string.isRequired 대신 rating: PropTypes.number.isRequired 로 교체
      Console탭을 확인해 보면 prop type 경고 메시지가 사라져 있다.

- 다른 종류의 prop-types 경고 해결하기

      Picture props의 이름을 image로 바꿔보자.
      화면에 사진이 나오지 않게 된다.
      Console 탭을 살펴보면 그 이유를 알 수 있다.
      Food 컴포넌트에 picture라는 이름의 props가 필요한데, 그 값이 undefined다.

- 코드 원래대로 돌려 놓기

      이럴 경우도 props오류가 발생한 다는 생각하며, 오류 메시지를 눈으로 익혀 둔다.

- isRequired의 뜻 살펴보기

      rating props는 필수가 아니어도 되는 항목이다.
      다만 값이 전달되는 경우 자료형이 number이기는 해야 한다.

#### state로 숫자 증감 기능 만들어 보기

      props는 정적인 데이터만 다룰 수 있다.
      state는 동적인 데이터를 다루기 위해 사용된다.
      state는 class형 컴포넌트에서 사용된다.
      기존의 App.js는 04-App.js로 이름을 바꾸고 새로운 App.js 파일을 생성한다.

- 클래스형 컴포넌트 작성하기.

-  App클래스가 React.Component 클레스를 상속 받도록 하자.

            class를 `React.Component`로 상속받아 생성한다.
            `React.Component` 클래스는 500여 줄이 넘는 코드로 여러가지 기능이 구현되있기 때문에 사용하기에 편리하다.
            import할 때 `{Component}`를 써주면 extends에서 `React.`을 생략할 수 있다.

-  render() 함수를 사용해보자.

            App컴포넌트가 JSX를 반환해야 하지만 class형 컴포넌트에서는 바로 return을 사용할 수 없다.
            render( ) 함수 내에서 return문을 사용한다.
            함수형 컴포넌트는 return문이 JSX를 반환하지만, 클래스형 컴포넌트는 render()함수가 JSX를 반환한다.

- state 정의하기
- state에 count값 추가하고 사용하기. 

      class안에 state = { }라고 작성하여 state를 정의한다.
      state는 객체형태의 데이터이다.
      state를 사용하려면 반드시 class형 컴포넌트 안에서, 소문자를 사용하여 state라고 적는다.
      state에 count키와 키값을 0으로 추가한다.
      return함수로 count값을 반환한다. -> rende함수에서 this.state.count를 출력한다.

- 버튼으로 count state값 변경하기

      <Add>버튼과 <Minus>번튼을 추가한다.
      JSX는 하나로 묶어야 함으로 <div>태스로 묶는다.
      버튼을 누를때 마다 console에 add와 minus가 출력되도록한다.
      함수는 화살표함수를 사용한다.
      Console을 통해 잘 작동하는지 확인한다.
      onClick속성을 통해 클릭이 일어날 때 마다 함수를 호출하도록 한다.
      Console에서 동작 여부를 확인해 본다.

## [ 9월 15일 학습내용 ]
>오늘 배운 내용 정리

- props 사용하기

      Props란 컴포넌트에서 컴포넌트로 전달하는 데이터를 말한다. 함수의 매개변수 역할을 하는 것이다. 따
      라서 props를 사용하면 컴포넌트를 효율적으로 사용할 수 있다.

- 비슷한 컴포넌트 여러 개 만들기 (효율적으로 컴포넌트를 출력하는 방법)

- map() 함수로 컴포넌트 많이 만들기 

      1. map함수 사용법 알아보기
            크롬에서 F12를 통해 개발자도구를 열고, console 탭으로 이동한다.
            friends라는 배열을 선언하고 친구 이름을 입력한다. (한글도 무관)
            만일 node가 설치되어 있다면 node를 사용해도 된다

      2. 배열의 이름을 입력하면 데이터를 확인할 수 있다.
      3. map() 함수 사용
            map()함수는 배열의 모든 원소 마다 특정 작업을 하는 함수를 적용하고, 그 함수가 반환한 결과를 모아서 배열로 반환해 준다.

## [ 9월 8일 학습내용 ]
>오늘 배운 내용 정리

- 리액트 앱 실행하는법

      CMD 에서 npm start 명령어 입력

- 리액트 앱 종료하는법

      ctrl + c 누르면 리액트 앱 종료

- package.json 파일 수정하기

      test, eject 명령어는 사용하지 않음으로 삭제

### Potato 컴포넌트 만들기


      - src폴더 안에 Potato.js라는 이름의 새 파일을 만든다.
      
      - 첫 글자는 반드시 대문자를 사용한다.

      - import 구문을 입력해야 리액트가 JSX를 이해할 수 있으니 반드시 입력한다.


- Potato() 함수 작성하기

      import React from 'react';

      function Potato() {
        
      }

- Potato JSX 반환

      import React from 'react';

      function Potato() {
        return <h3> I love potato</h3>;
      }

      export default Potato;

### Potato 컴포넌트 사용
