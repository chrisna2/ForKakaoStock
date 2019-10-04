# 특정 고객 거래내역 조회 서비스 개발 명세
### For Kakao Stock! 

## 사용한 개발 프레임워크

1. Spring Boot
> * Mybatis + MySql JDBC
> * Spring boot starter aop
> * Spring boot starter web
> * Spring boot starter test + hamcrest
> * html + Jquery
> * logback

2. AWS EC2 linux + MySQL DBMS
> * 접속 정보 IP : 52.79.240.193:3306 


## 문제 해결 방법
### 서비스 개발 사전 작업 : Databasea및 프레임워크 구축
1. 데이터를 어디에서도 확인이 가능 하도록 Database를 구축해야 했습니다.
AWS EC2 리눅스(프리티어) 서버를 구축하고 MySql DB를 구축하여 해당 접속 정보를 
properties.yml에 등록했습니다. 현재 이 해당 깃 허브를 통해 소스를 다운 받으시거나
빌드된 JAR파일을 보유하고 있다면 언제든 해당 DB에 접속이 가능합니다.
하지만 로컬의 DB를 구축한 상황을 가정해서 제가 구축한 DB의 schema.sql도 프로젝트에 함께 동봉했습니다. (src/main/resource/)


2. 기본적으로 개발을 진행하기 위해서 프로젝트 내에 일을 구성할 수 있는 프래임워크가 구축이 되어야 합니다. 가장 일반적으로 필요한 것은 로그처리와 예외처리, 그에 대한 메세징처리, DataSource 설정, 프로젝트 디자인이 대표 적일 것입니다.

> * 로그 처리 : 기본적인 플러그인은 logback을 사용 하였습니다. AOP 클래스를 활용하여 컨트롤러 호출시 마다. 호출된 컨트롤러 클래스와
메서드를 로그에 띄우고 컨트롤러 호출이 완료되어 화면에 리턴하는 데이터를 로그에 출력하도록 했습니다. 
SQL 출력로그는 제가 주로 사용하는 인터셉트 클래스를 활용하였고 현재 프로젝트에 맞게 간소화 시켜 사용했습니다. 현재 실행되는 쿼리의
내용과 그 SQL 처리 결과를 로그로 출력하는 기능을 가지고 있습니다.
(해당 인터셉트 클래스에 경우 제가 아는 고급개발자 한 분과 함께 배우고 개발하면서 만들어낸 소스로서 저에게는 아주 뜻 깊은 소스이기도 합니다. ^_^)

> * 예외 처리 : kakaoException 이라는 이름으로 예외 객체를 구성했으며 RuntimeException을 상속받았습니다. 원래는 문제 4번을 생각하고 만들어낸건데
httpStatus의 값을 바꾸는 부분을 고려하지 않아서 현재 프로젝트에서는 사용되지 않고 있는 상태 입니다.

> * 메세징 처리 : kakaoException에 경우 단순히 예외처리 뿐 아니라 화면에 에러 메세지를 출력하는 기능도 고려 했기 때문에 예외가 발생시 
해당 예외 내용에 대한 메세지를 출력하는 Handler클래스가 따로 구성했습니다. @ControllerAdvice REST API로서 에러가 발생시 그에 대한 메세지를
출력 하도록 설정 했습니다. kakaoException외에 MyBatisSystemException, SQLException에 대한 예외 발생시 해당 예외에 대한 메세징을 뿌려주는 
기능을 담당합니다. 만약 메세지 테이블을 구성했다면 메세지코드로 해당하는 메세지를 뿌려주는 역활을 수행해야 합니다만 현재는 아주 간단하게 
구성해 봤습니다.

> * DataSource 설정 : Spring Boot 프로젝트에서 JPA가 아닌 Mybatis를 사용한 이유는 저에게 가장 익숙해서입니다. 
최대한 짧은 시간에 과제를 제출하고 싶었고 그걸 위해서 저에게 가장 익숙한 Mybatis를 활용했습니다. 
JPA의 활용성을 모르지는 않지만 문제를 풀기위해 활용될 JAVA 람다식 프로그래밍보다 쿼리로 처리하는 것이 저에게는 익숙하고 편했기 때문입니다. 
MySql을 사용했기 때문에 mysql jdbc를 설정했습니다.

> * 프로젝트 디자인 : 가장 일반적인 MVC 패턴을 활용하여 구축하였습니다. 그리고 추가적으로 저는 DTO(VO)형 클래스가 남발되는 것을 별로 좋아하지 않습니다. 데이터의 형태가 항상 늘 클래스에 고정되지 않기 때문입니다. 따라서 현재 제 프로젝트에는 DTO 클래스가 없습니다. 대부분의 파라미터는 HashMap 형태입니다.

### 문제 해결 방법

1. 




## windows docker -> linux docker [branch : mylinux]
> 윈도우 도커로는 실제 웹 배포 환경을 구축하기 대단히 어려워서 가상화 서버를 통해 배포 시스템을 구축해 보기로 했다

1. vmware로 centos 가상화 구축
2. 리눅스 docker 설치
  ```
  1. Install => yum install docker
  2. 서비스 실행 => service docker start
  3. 부팅시 자동 실행 설정 => chkconfig docker on
  ```
3. 필요한 프로그램 설치 : git, maven (지금까진 이정도면 충분하다.)
4. git clone https://github.com/chrisna2/msa_in_docker.git
5. mvn clean package docker:build
6. [수정 지점] : docker-compose 파일의 볼륨을 설정할 경우 
> window-docker에는 host volume을 외부 볼륨(external)으로 따로 경로설정없이 이름으로 구성


# 추후 추가로 진행해야 할 것들
1. ~~7장과 8장을 같이 융합~~ 그냥 하니까 잘된다. 책을 다시 확인해보니 검증 서비스 영역과 비검증 서비스 영역으로 나눠애 도리것 같다. 그와 같이 서비스 게이트 웨이도 따로 만들어 줘야 할 것 같다.
2. ~~로그인을 검증정보 DB를 따로 만들야 될것 같다.~~ 
(오라클로 설치함, 그러나 로그인 정보는 이미 mariadb로 구축이 완료 되어서 불필요함 이건 패스)
3. 현재 MSA 환경에 파이썬을 끼얺어 봐야 할것 같다.
4. ~~9장 분산 로그 환경 구성~~ (완료)
5. 10장 AWS 배포 까지.
6. 리눅스 서버에 docker를 구축하고 실제 MSA CICD 구성
7. ui msa 서비스를 구성해 본다. msa-sa06-bnkuisvc
8. ~~리눅스로 옮기고 나서 집킨에 서비스가 제대로 등록되지 않고 있다.~~ (확인:서비스가 제대로 안올라간 것이었다.)
9. logo

