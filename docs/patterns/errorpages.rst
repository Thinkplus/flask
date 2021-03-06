커스텀 오류 페이지
==================

플라스크에는 앞에서 나온 HTTP 오류 코드를 가지고 요청을 중단하는 
:func:`~flask.abort` 함수가 있다.  그것은 또한 정말 꾸미지 않은 기본적인 
설명을 가진 단순한 흑백의 오류 페이지를 제공할 것이다.

오류 코드에 따라서 사용자가 실제로 그런 오류를 볼 가능성이 있다. 

공통 오류 코드
--------------

다음의 오류 코드는 어플리케이션이 정상적으로 동작했음에도 불구하고 사용자에게 
종종 보여지는 것이다:

*404 Not Found*
    즐겨쓰는 메시지에 "이봐 친구, 그 URL 입력에 실수가 있어" 가 있다.
    인터넷 초보자 조차 그 404를 알고 있는 그렇게 일반적인 것은 다음을
    의미한다 : 젠장, 내가 찾고 있는 것이 거기에 없네.  404 페이지에 
    적어도 index 페이지로 돌아갈 수 있는 링크와 같은 유용한 것이 있도록
    하는게 매우 좋은 방식이다.

*403 Forbidden*
    여러분의 웹사이트에 어떤 접근 제어가 있다면, 허용되지 않는 자원에 대해
    403 코드를 보내야할 것이다.  그렇기 때문에 사용자가 금지된 자원에 대해 
    접근하려할 때 사용자가 링크를 잃어버리지 않도록 해야한다. 

*410 Gone*
    여러분은 "404 Not Found" 에게 "410 Gone" 이라는 형제가 있었다는 것을 
    알았는가?  일부 사람들만 실제로 그것을 구현했지만, 그 방식은 전에
    존재했지만 현재 삭제된 자원에 대해 404 대신에 401 로 응답하는 것이다.
    여러분이 데이터베이스에서 영구적으로 문서를 지우지 않고 삭제됐다고 
    표시만 한다면, 사용자에게 편의를 제공하며 410 코드를 대신 사용하고 
    그들이 찾고 있는 것은 영구적으로 삭제됐다는 메시지를 보여줘라.

*500 Internal Server Error*
    보통 프로그래밍 오류나 서버에 한계 부하를 넘었을 때 이 오류가 발생한다.
    그 경우에 멋진 페이지를 보여주는 것이 굉장히 좋은 방식인데, 왜냐하면
    여러분의 어플리케이션은 머지않아 다시 동작하지 않을 것이기 때문이다
    (여기를 또한 살펴봐라: :ref:`application-errors`).


오류 핸들러
-----------

오류 핸들러는 뷰 함수와 같은 일종의 함수이지만, 그것은 오류가 발생하고
그 오류를 전달했을 때 호출된다.  오류는 대부분 
:exc:`~werkzeug.exceptions.HTTPException` 이지만, 어떤 경우에는 다른 
오류일 수도 있다: 내부 서버 오류에 대한 핸들러는 그 요류들이 잡히지 않을지
모르지만 다른 예외 인스턴스에 넘겨질 것이다.

오류 핸들러는 :meth:`~flask.Flask.errorhandler` 데코레이터와 예외에 대한 
오류 코드를 가지고 등록된다.  플라스크는 오류 코드를 설정하지 *않을 것*
이라는 것을 명심하고, 그렇기 때문에 응답을 반환할 때 HTTP 상태 코드를 
제공하도록 해야한다.

여기에 "404 Page Not Found" 예외에 대한 구현 예제가 있다::

    from flask import render_template

    @app.errorhandler(404)
    def page_not_found(e):
        return render_template('404.html'), 404

예제 템플릿은 다음과 같을 것이다:

.. sourcecode:: html+jinja

   {% extends "layout.html" %}
   {% block title %}Page Not Found{% endblock %}
   {% block body %}
     <h1>Page Not Found</h1>
     <p>What you were looking for is just not there.
     <p><a href="{{ url_for('index') }}">go somewhere nice</a>
   {% endblock %}
