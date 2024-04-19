## HTTP 통신

### HTTP로 통신하는 방법

HTTP로 통신할 때 클라이언트는 **HTTP 메서드**를 사용하여 요청 메시지에 넣어 전달하고, 서버는 요청에 대한 **상태 코드**를 응답 메시지에 넣어 전달합니다.

### HTTP 상태코드

클라이언트가 HTTP 메서드 중 하나로 서버에 요청을 보낼 경우 서버는 요청에 대한 응답 코드를 상태 코드와 함께 전달합니다.

이때 **상태 코드**는 HTTP 요청에 대한 응답 결과를 나타냅니다. 개발자는 상태 코드를 보고 요청이 성공했는지 혹은 실패했는지 판단하고 적절하게 처리할 수 있습니다.

상태코드는 5개의 그룹으로 나뉩니다.

| 그룹 | 상태코드             | 설명                                                                           |
| ---- | -------------------- | ------------------------------------------------------------------------------ |
| 1xx  | 정보 응답            | 요청에 대한 처리가 아직 진행 중이라는 의미입니다.                              |
| 2xx  | 성공 응답            | 요청에 대한 응답을 성공적으로 완료했다는 의미입니다.                           |
| 3xx  | 리다이렉션 메시지    | 요청이 완료하기 위해 리다이렉션(새 URL로 재요청)이 필요하다는 의미입니다.      |
| 4xx  | 클라이언트 오류 응답 | 요청을 처리하던 중 클라이언트 오류가 발생했다는 의미입니다.                    |
| 5xx  | 서버 오류 응답       | 클라이언트의 요청을 받았으나 적절히 처리하지 못해 응답할 수 없다는 의미입니다. |

### Spring에서 상태코드를 적용하는 방법

Spring Boot에서는 간단한게 상태 코드를 적용할 수 있습니다.

```java
@WebServlet(name="HeaderServlet", urlPatterns = "/res-header")
public class HeaderServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException

    //상태코드 세팅
    response.setStatus(HttpServletResponse.SC_OK); //HTTP response 응답코드 지정

    //응답 header
    response.setHeader("Content-Type", "text/plain;charset=utf-8");
    response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate"); // cache 무효화
    response.setHeader("test-header", "test-header" );//내가 원하는 헤더를 생성합니다.
    response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");

    //body
    PrintWriter writer = response.getWriter();
    writer.println("success");
}
```

### HTML 파일을 리턴할 경우

HTML 파일을 리턴할 경우 기존에 작성했던 부분에서 ContentType만 변경해서 내보내주면 됩니다.

하지만 보는바와 같이 직접적으로 작성하는 것이 힘든 것을 알 수 있습니다.

```java
@WebServlet(name="HeaderServlet", urlPatterns = "/res-header")
public class HeaderServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException

    //상태코드 세팅
    response.setStatus(HttpServletResponse.SC_OK); //HTTP response 응답코드 지정

    //응답 header
    response.setHeader("Content-Type", "text/html");
    response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate"); // cache 무효화
    response.setHeader("test-header", "test-header" );//내가 원하는 헤더를 생성합니다.
    response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");

    //body
    PrintWriter writer = response.getWriter();
    writer.println("<html>");
    writer.println("<body>");
    writer.println("<div>테스트</div>");
    writer.println("</body>");
    writer.println("</html>");
}
```

때문에 Html 파일에 연결을 해주기 위해 링크를 변경하거나 혹은 다른 서블릿으로 연결을 하여 JSP 파일을 보여주는 방식을 택합니다.

```java
// 클라이언트에게 새로운 URL로 이동하라는 명령이기 때문에 사용자가 변화를 알아챕니다.
response.sendRedirect("/basic/post-form.html");

// 서버내에서 요청을 다른 서블릿으로 보내는 것이기 때문에 사용자가 눈치채지 못합니다.
request.getRequestDispatcher("/another-servlet").forward(request, response);
```

```java
@WebServlet(name="AnotherServlet", urlPatterns = "/another-servlet")
public class AnotherServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException

    String viewPath = "/WEB-INF/post-form.jsp";

    // forward로 바라보았기 때문에 주소의 변화는 일어나지 않습니다.
    RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);

    dispatcher.forward(request, response);
}
```
