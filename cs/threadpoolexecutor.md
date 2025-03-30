# Thread Pool

## Thread Per Request Model
하나의 요청(Request)을 하나의 쓰레드가 맡아 처리하는 모델을 뜻한다. 대표적으로 Spring Boot에 내장되어 있는 Tomcat이 있다. 

만약 서버에서 들어오는 요청마다 