
- 스프링에서는 DispatcherServlet이 ViewName으로 적절한 View 객체를 생성한다.
- View 객체 생성은 ViewResolver 안에서 이루어진다.
- 이렇게 생성된 View는 render 메서드를 통해 model을 주입받아 응답을 적절한 형태로 렌더링한다.
- 당연하게도 Bean 기반의 ViewResolver도 존재한다.