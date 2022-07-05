# 작업 환경 설정

1. Node.js / npm, yarn 설치

   - **Node.js 설치**

     - macOS, Ubuntu

     ```bash
     $ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
     ```

     ```bash
     $ nvm --version
     ```

     > nvm 설치 확인 

     ```bash
     $ vim ~/.bash_profile  (macOS)
     $ vim ~/.bashrc  (Ubuntu)
     ```

     > 터미널 재시작 시, 버전이 나타나지 않는 경우

     ```bash
     $ nvm install --lts
     ```

     - windows (공식홈페이지에서 Downloads)

     ```bash
     $ node -v
     ```

     > Node.js 설치 확인

     ---

   - **yarn 설치**

     ```bash
     $ npm install --global yarn
     $ yarn --version
     ```

     ---

     ➕ `바벨 (babel)` : ECMAScript6을 호환시켜준다.

     ➕`웹팩(webpack)` : 모듈화된 코드를 한 파일로 합치고 (번들링) 코드를 수정할 때마다 웹 브라우저를 리로딩하는 등 여러기능을 지님

     ---

     

2. 코드에디터 설치

3. Git 설치

4. create-react-app으로 프로젝트 만들기

   ```bash
   $ yarn create react-app <프로젝트 이름>
   ```

   ```bash
   $ npm init react-app <프로젝트 이름>
   ```

   > yarn을 사용하지 않는 경우

   ```bash
   $ yarn start   # 또는 npm start
   ```

   > 프로젝트 디렉터리로 이동한 뒤 브라우저 실행 `http://localhost:3000/`

