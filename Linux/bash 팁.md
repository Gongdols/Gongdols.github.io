# 참고 자료
* https://www.cyberciti.biz/faq/linux-list-all-environment-variables-env-command/
* https://linuxize.com/post/how-to-set-and-list-environment-variables-in-linux/
* https://askubuntu.com/questions/682120/what-does-colon-operator-in-a-bash-variable-expansion-var-temp3

# 변수
**이 항에서는 메모리 상에서 변수를 다루는 방법을 설명한다.**

시스템의 기본(default) 변수를 다루는 방법은 아래의 [기본 환경 변수](#기본%20환경%20변수)을 항을 참고하면 된다.
- ## 쉘 변수
  쉘 변수는 **현재 사용하고 있는 쉘 환경**에만 적용되는 변수이다.

  쉘 변수를 변경하면 **즉시** 변경사항이 현재 쉘에 적용된다.
  - 변수 설정
    ```sh
    # 변수 A에 숫자 1 설정
    $ set A=1
    $ A=1 # set 생략 가능

    # 변수 B에 문자열 "TEST" 설정
    $ set B="TEST"
    $ B=TEST # "" 생략 가능
    ```
  - 변수 조회
    ```sh
    # 모든 변수
    $ set
    $ set | less # 파이프 사용 가능

    # 변수 A의 값
    $ echo ${A}
    $ echo $A # {} 생략 가능
    ```
  - 변수 참조

    아래의 [변수 참조](#변수%20참조) 항 참고
  - 변수 제거

    아래의 [변수 제거](#변수%20제거) 항 참고
- ## 환경 변수
    환경 변수는 **시스템 전역**에 적용되는 변수이다.
  - 변수 설정
    ```sh
    # 쉘 변수 A를 환경 변수로 변경
    $ export A

    # 환경 변수 A에 값 1 설정
    $ export A=1
    $ export A="1" # 정수형과 문자형의 구분 없음

    # 환경 변수 C에 배열 설정
    $ export B="TEST"
    $ export B=TEST # "" 생략 가능
    ```
  - 변수 조회
    ```sh
    # 모든 변수
    $ env
    $ env | less # 파이프 사용 가능
    $ printenv
    $ printenv | less # 파이프 사용 가능

    # 특정 환경 변수의 값 조회
    $ printenv A
    $ printenv A, B # 한꺼번에 여러개 조회 가능
    $ bash -c 'echo $A'
    $ echo $A # 가능하지만 환경 변수인지 확인 불가능
    ```
  - 변수 참조

    아래의 [변수 참조](#변수%20참조) 항 참고
  - 변수 제거

    아래의 [변수 제거](#변수%20제거) 항 참고
## 변수 참조
  ```sh
  # 변수 A의 값 참조
  $ ${A}
  $ $A # {} 생략 가능
  $ "{$A}" # "" 사용 가능
  $ "$A" # {} 생략 가능
  $ '{$A}' # '' 사용 불가능

  # 변수 참조 - 확장
  $ x=1234567890
  $ echo ${x:3}
  4567890
  $ echo ${x:7}
  890
  $ echo ${x:3:5}
  45678

  # 다른 텍스트 변수 A와 조합
  $ 다른${A}텍스트 # 가능
  $ 다른 텍스트$A # 가능
  $ 다른$A텍스트 # 오류
  $ "다른${A}텍스트" # 가능
  $ "다른 텍스트$A" # 가능
  $ "다른$A"텍스트 # 오류
  ```
## 변수 제거
  ```sh
  # 변수 A 제거
  $ unset A
  ```
# 기본 환경 변수
# (보류)
- ## `/etc/environment`을 수정하는 방법
  가장 직접적으로 기본 환경 변수를 설정하는 방법.
  ## .bashrc를 수정하는 방법