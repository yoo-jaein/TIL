# Command
자주 쓰는 Linux 명령어 모음  

## ls
현재 위치의 파일 목록을 조회하는 명령어  

### 주요 옵션 (각 옵션들은 합성해서 사용가능)
- ls -l : 파일들의 상세 정보를 나타낸다.  
- ls -a : 숨어있는 파일들도 표시한다.  
- ls -t : 파일을 최신 것부터 정렬하여 표시한다.  
- ls -rt : 파일을 오래된 것부터 정렬하여 표시한다.  
- ls -F : 파일을 표시할 때 마지막에 타입명을 끝에 표시한다.    
    - '/' : 디렉토리  
    - '*' : 실행 파일  
    - '@' : 링크  
- ls -lrt : 파일들의 상세 정보를 나타내며, 오래된 것부터 표시한다.  


## cd
경로를 이동할 때 사용하는 명령어  

### 사용법
- cd ~ : 홈 디렉토리로 바로 이동한다.  
- cd .. : 상위 디렉토리로 이동한다.  
- cd /dir : 절대경로 dir로 이동한다.  
- cd - : 이동하기 바로 전의 디렉토리로 이동한다.  


## touch
파일의 용량이 0인 파일을 생성하거나 파일의 날짜 정보를 변경하는 명령어

### 사용법
- touch filename : filename의 파일을 생성한다.  
- touch -c filename : filename의 시간을 현재시간으로 변경한다.  
- touch -t 200001011200 filename : filename의 시간을 날짜정보(YYYYMMDDhhmm)로 변경한다.  
- touch -r filename1 filename2 : filename2의 날짜정보를 filename1의 날짜정보와 같게 변경한다.  


## mkdir
새로운 디렉토리를 만들 때 사용하는 명령어  

### 사용법
- mkdir dirname : dirname의 디렉토리를 생성한다.  
- mkdir -p dirname/subdname : 존재하지 않는 디렉토리의 하위 디렉토리까지 생성한다.  
- mk -m 644 dirname : 특정 퍼미션을 갖는 디렉토리를 생성한다.  


## cp
파일을 복사하는 명령어

### 사용법
- cp file cfile : file을 cfile이라는 이름으로 복사한다.  
- cp -f file cfile : 복사할 때 복사대상이 있으면 지우고 강제로 복사한다. (덮어쓰기)  
- cp -R dir cdir : 디렉토리를 복사할 때 사용한다. 폴더 안의 모든 하위 경로와 파일들을 복사한다.  


## mv
파일을 이동하는 명령어

### 사용법
- mv fname mfname : fname의 파일을 mfname의 이름으로 이동/변경한다.  
- mv -b fname mfname : mfname의 파일이 존재하면 mfname을 백업한 뒤에 이동한다.  
- mv -f fname mfname : mfname의 파일이 존재하면 백업 없이 덮어쓴다.  


## rm
파일이나 디렉토리를 삭제할 때 사용하는 명령어

### 사용법
- rm fname : fname을 삭제한다.  
- rm -f fname : fname을 강제로 삭제한다.  
- rm -r dir : dir을 삭제한다. 디렉토리는 -r 옵션 없이는 삭제할 수 없다.  


## cat
파일 이름을 인자로 받아서 그 내용을 출력할 때 사용하는 명령어

### 사용법
- cat fname : fname의 내용을 출력한다.  
- cat fname1 fname2 : fname1과 fname2의 내용을 이어서 출력한다.  
- cat fname1 fname2 | more : fname1, fname2를 출력하는데 페이지별로 출력한다.  
- cat fname1 fname2 | head : fname1, fname2를 출력하는데 처음부터 10번째까지만 출력한다.  
- cat fname1 fname2 | tail : fname1, fname2를 출력하는데 끝에서부터 10번째까지만 출력한다.  


## redirection ('>', '>>')
리눅스 스트림의 방향을 조절하는 명령어

### 사용법
- 명령 > 파일 : 명령의 결과를 파일로 저장한다.  
    - cat fname1 fname2 > fname3 : fname1, fname2를 출력하고 fname3이라는 파일에 저장한다.  
- 명령 >> 파일 : 명령의 결과를 파일에 추가한다.  
    - cat fname4 >> fname3 : fname3에 fname4의 내용을 추가한다.  
- 명령 < 파일 : 파일의 데이터를 명령에 입력한다.  
    - cat < fname1 : fname1의 내용을 출력한다.  


## alias
자주 사용하는 명령어를 간단한 명령어로 설정하는 명령어

### 사용법
- alias new = 'command' : command를 실행하는 새 명령어 new를 만든다.  
    - alias ls='ls -l' : ls를 실행하면 -l 옵션을 갖는 ls를 실행한다.  
- alias : 현재 alias 목록을 출력한다.  
- unalias new : new라는 alias를 해제한다.  


