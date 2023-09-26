# Qt for Python

출처: https://doc.qt.io/qtforpython-6/quickstart.html

## 빠른 시작

#### 요구사항

Qt for Python을 설치하기 전에 다음 소프트웨어를 먼저 설치해야 합니다.

* Python 3.7 이상
* venv 또는 virtualenv와 같은 가상 환경을 사용할 것을 권장함, 그리고 당신의 시스템에 pip로 PySide6를 설치하지 않을 수 있습니다.

#### 설치

* 터미널에서 다음과 같이 실행하여 환경을 만들고 활성화합니다.

  - 환경 만들기: `python -m venv env`

  - 환경 활성화하기 (Linux와 macOS): `source env/bn/activate`

  - 환경 활성화하기 (Windows): `env\Scripts\activate.bat`

* PySide6 설치하기

이제 pip를 이용하여 Qt for Python 패키지를 설치합니다. 터미널에서 다음 커맨드를 실행하십시오.

  - 최신 버전의 경우: `pip install pyside6`

  - 특정 버전의 경우: `pip install pyside6==6.4.1`

* 설치 확인하기

설치가 잘 되었으면 다음 커맨드를 통해 버전을 확인할 수 있습니다.

```
import PySide6.QtCore

# PySide6 버전 출력
print(PySide6.__version__)

# PySide6를 컴파일하는 데 사용된 Qt 버전을 출력
print(PySide6.QtCore.__version__)
```

#### 간단한 Qt Widgets 애플리케이션 만들기

다음은 "Hello World"를 출력하는 간단한 애플리케이션입니다.

```python
import sys
import random
from PySide6 import QtCore, QtWidgets, QtGui

class MyWidget(QtWidgets.QWidget):
    def __init__(self):
        super().__init__()

        self.hello = ["Hallo Welt", "Hei maailma", "Hola Mundo", "Привет мир"]

        self.button = QtWidgets.QPushButton("Click me!")
        self.text = QtWidgets.QLabel("Hello World",
                                     alignment=QtCore.Qt.AlignCenter)

        self.layout = QtWidgets.QVBoxLayout(self)
        self.layout.addWidget(self.text)
        self.layout.addWidget(self.button)

        self.button.clicked.connect(self.magic)

    @QtCore.Slot()
    def magic(self):
        self.text.setText(random.choice(self.hello))

if __name__ == "__main__":
    app = QtWidgets.QApplication([])

    widget = MyWidget()
    widget.resize(800, 600)
    widget.show()

    sys.exit(app.exec())
```

#### 간단한 Qt Quick 애플리케이션 만들기

이것은 간단한 형태로 만든 QML 코드이지만, 일반적으로 .qml 파일로 나눠서 작성해야 합니다.

```python
import sys
from PySide6.QtGui import QGuiApplication
from PySide6.QtQml import QQmlApplicationEngine

QML = """
import QtQuick
import QtQuick.Controls
import QtQuick.Layouts

Window {
    width: 300
    height: 200
    visible: true
    title: "Hello World"

    readonly property list<string> texts: ["Hallo Welt", "Hei maailma",
                                           "Hola Mundo", "Привет мир"]

    function setText() {
        var i = Math.round(Math.random() * 3)
        text.text = texts[i]
    }

    ColumnLayout {
        anchors.fill:  parent

        Text {
            id: text
            text: "Hello World"
            Layout.alignment: Qt.AlignHCenter
        }
        Button {
            text: "Click me"
            Layout.alignment: Qt.AlignHCenter
            onClicked:  setText()
        }
    }
}
"""

if __name__ == "__main__":
    app = QGuiApplication(sys.argv)
    engine = QQmlApplicationEngine()
    engine.loadData(QML.encode('utf-8'))
    if not engine.rootObjects():
        sys.exit(-1)
    exit_code = app.exec()
    del engine
    sys.exit(exit_code)
```

### Qt, QML, Widgets... 무슨 차이가 있죠?

기본적으로 Qt는 C++로 작성되고 설계된 C++ 프레임워크입니다. 대부분의 레퍼런스, 예제, 개념들은 C++ 기반 애플리케이션입니다. 하지만 Qt for Python의 목표는 Python에 Qt 프레임워크를 적용시키는 것이기 때문에 C++에 대해 몰라도 됩니다.

#### Qt

Qt는 많은 컴포넌트를 갖고 있습니다. 가령 qtbase는 베이스 컴포넌트로서 다음과 같은 많은 모듈을 갖고 있습니다: QtCore, QtGui, QtWidgets, QtNetwork 등. 모듈에는 여러 클래스들이 있는데 당신은 여기서 직접 클래스를 골라서 사용할 수 있습니다. 가령 QtCore의 클래스에서 QFile, QTime, QByteArray 등을 꺼내쓸 수 있습니다.

커맨드 라인 애플리케이션, 파일 처리, 네트워크 연결, 정규 표현식, 텍스트 인코딩 등의 경우에는 굳이 GUI가 없이도 애플리케이션을 만들 수 있습니다.

그 외에는 Widgets이라고 하는 QtWidget 모듈의 클래스를 이용하여 그래픽 애플리케이션을 만들 수 있습니다.

여러 Qt 모듈 중에는 QtDeclarative라는 특별한 기능이 있는데 QML declarative 언어도 있습니다. 이 언어는 CSS와 JSON과 비슷하며 선언으로 UI 애플리케이션을 만들고 설계할 수 있으며 명령형 섹션에 JavaScript를 넣을 수 있고 컴포넌트를 확장하여 C++과 코드를 연결할 수도 있습니다.

#### Widgets

QtWidgets은 그래픽 애플리케이션에 추가할 수 있는 미리 정의된 위젯을 제공합니다. 가령 버튼, 라벨, 박스, 메뉴 등이 있습니다. 위젯 기반 애플리케이션은 네이티브 애플리케이션처럼 보일 것입니다.

#### QML

QML은 위젯과 다른 방식의 사용자 인터페이스를 만드는 접근법입니다. 본래 모바일 애플리케이션을 만들기 위한 것이었습니다. Qt Quick 모듈이 있으면 탭, 드래그 앤 드롭, 애니메이션, 상태, 전환, 드로워 메뉴 등 모바일 장치와 상호 작용할 수 있는 액세스를 제공합니다.

QML/Quick 애플리케이션에서 찾을 수 있는 요소들은 특정 동작을 기반으로 하는 다양한 프로퍼티를 가진 다이나믹한 애플리케이션 인프라를 제공하는 데 초점을 맞추고 있습니다.

물론 QML이 모바일 장치에 대한 인터페이스를 제공하는 것이 목적이지만 데스크톱 애플리케이션에도 사용할 수 있습니다.

또 표준 JavaScript로 애플리케이션을 강화할 수 있고 C++을 결합할 수 있으니 매력적인 인프라가 될 수 있습니다.

#### Python과 C++

Qt for Python의 경우 C++을 알 필요가 없습니다만, 다음과 같이 두 언어를 섞을 가능성도 있습니다.

1. Qt/C++ 애플리케이션을 갖고 있다면 Qt/Python 애플리케이션으로 다시 만들 수 있습니다. Qt 애플리케이션의 사용자 수준 C++ 코드를 완전히 Python 코드로 대체하는 것이 목표입니다.
2. C++에서 작성한 커스텀 Qt 위젯의 경우, Python 바인딩을 만들어서 Python에서 해당 위젯을 직접 사용할 수 있습니다.
3. 가령 성능이 중요한 특정 작업을 담당하는 C++ 기반 라이브러리를 갖고 있다면 바인딩을 만들어서 Python에서 사용할 수 있습니다.
4. Qt/C++ 애플리케이션의 경우, 메인 QApplication 싱글톤을 Python 인터프리터에 대한 Python 바인딩으로 노출시켜서 Python으로 확장할 수 있습니다.

2,3,4의 경우 바인딩 생성 도구인 Shiboken의 도움을 받아서 Qt for Python을 생성할 수 있습니다.

### 어떤 IDE가 호환됩니까?

Python이 호환되는 IDE에서 Qt for Python을 사용할 수 있지만 전부 다 Qt Creator와 같은 기능을 제공해 주지는 않습니다.

파일 작성 외에도 애플리케이션 개발에 도움을 받기 위해 추가 단계가 있습니다.

터미널에서
* `.ui` 파일에서 Python 파일 생성하기: `pyside6-uic -i form.ui -o ui_form.py`
* `.qrc` 파일에서 Python 파일 생성하기: `pyside6-rcc -i resources.qrc -o rc_resources.py`
* `.ui` 파일을 편집/생성하기 위해 커맨드 `pyside6-designer`로 Qt Designer 열기

당신이 선호하는 IDE에 외부 애드온/플러그인이 이 커맨드를 실행하기 위한 구성 단계를 포함시킬 수도 있고, Designer와 QtCreator와 같은 도구를 사용할 수도 있습니다.

#### QtCreator

QtCreator에서 사용할 수 있는 기본 템플릿을 기반으로 새로운 프로젝트를 만들 수 있습니다. 템플릿을 선택한 후에 프로젝트 이름, 인터페이스에 사용할 베이스 클래스 등 세부사항을 지정할 수 있습니다.

#### Visual Studio Code

[비공식 플러그인](https://marketplace.visualstudio.com/items?itemName=seanwu.vscode-qt-for-python)을 사용하여 코드 편집 외에도 추가 기능을 활성화할 수 있습니다.

#### PyCharm

PyCharm에서 Qt Designer와 Qt Creator와 같은 외부 도구를 사용할 수 있도록 구성할 수 있습니다. `File > Settings > tools > PyCharm External Tools`에 가서 다음 정보를 프로젝트에 포함시키세요. 나중에 `.ui` 파일을 우클릭하고 `Qt Designer`, `pyside6-uic`를 선택할 수 있습니다.

### 바인딩 생성: Shiboken이 무엇입니까?

`PySide6`를 설치할 때 `Shiboken6`가 의존성으로 설치되는 것을 보실 수 있습니다.

설치된 이 패키지를 Shiboken 모듈이라고도 합니다. 그리고 이것은 PySide가 제대로 작동하기 위한 유틸리티도 가지고 있습니다. 자세한 정보는 [여기](https://doc.qt.io/qtforpython/shiboken6/shibokenmodule.html)를 참조하시기 바랍니다.

PySide를 설치할 때 설치되지 않는 제3의 패키지가 있는데 이것은 필수가 아니며 Shiboken Generator라고 합니다.

"Shiboken"을 사용하라거나 "바인딩 생성"과 관련된 것들의 대부분은 PySide 패키지의 의존성으로 설치되는 Shiboken 모듈이 아닌 Shiboken Generator를 가리키는 것입니다.

#### SHiboken Generator가 필요한가요?

만약 Python에서 작동하는 Qt 애플리케이션을 만드는 것이 목적이라면 굳이 Shiboken generator를 설치할 필요가 없습니다. 그러나 Qt/C++ 애플리케이션과 Python을 바인딩하고 싶다면 그것이 필요할 것입니다.

Shiboken과 관련된 정보는 [여기](https://doc.qt.io/qtforpython/shiboken6/)서 찾을 수 있습니다.

### 파일 타입

Qt for Python 애플리케이션을 개발하면서 만나게 되는 ui, grc, qml, pyproject 등 여러 가지 파일 타입이 있습니다. 다음은 이것에 대한 간단한 설명입니다.

#### Python 파일 `.py`

Qt for Python 프로젝트를 개발하면서 주로 다루게 되는 Python 파일입니다.

`.ui`, `.grc`, `.qml` 파일 없이 Python 파일만으로 애플리케이션을 작성하는 것이 가능합니다. 하지만 다른 파일 포맷을 사용하면 개발 과정이 용이해지거나 애플리케이션에 새로운 기능을 넣을 수 있습니다.

```python
class MyWidget(QWidget):
    def __init__(self):
        QWidget.__init__(self)

        self.hello = ["Hallo Welt", "你好，世界", "Hei maailma",
            "Hola Mundo", "Привет мир"]

        self.button = QPushButton("Click me!")
        self.text = QLabel("Hello World")
        self.text.setAlignment(Qt.AlignCenter)
        # ...
```

#### 사용자 인터페이스 정의 파일 `.ui`

Qt Designer를 사용할 때 WYSIWYG 폼 편집기를 이용하여 Qt Widgets으로 사용자 인터페이스를 만들 수 있습니다. 이 때 인터페이스는 XML을 사용하는 위젯 트리로 표현됩니다. 다음은 `.ui` 파일의 일부 예시입니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ui version="4.0">
 <class>MainWindow</class>
 <widget class="QMainWindow" name="MainWindow">
  <property name="geometry">
   <rect>
    <x>0</x>
    <y>0</y>
    <width>400</width>
    <height>300</height>
   </rect>
  </property>
  <property name="windowTitle">
   <string>MainWindow</string>
  </property>
  <widget class="QWidget" name="centralWidget">
...
```

pyside6-uic 도구는 이 .ui 파일에서 Python 코드를 생성하므로 배포된 애플리케이션에 .ui 파일을 포함시킬 필요가 없습니다.

#### 리소스 컬렉션 파일 `.qrc`

애플리케이션과 함께 사용되는 바이너리 파일 목록입니다. XML-기반 파일이며 구조는 다음과 같습니다.

```xml
<!DOCTYPE RCC><RCC version="1.0">
<qresource>
    <file>images/quit.png</file>
    <file>font/myfont.ttf</file>
</qresource>
</RCC>
```

pyside6-rcc 도구는 .qrc 파일에서 Python 코드를 생성하므로 배포된 애플리케이션에 목록에 있는 파일들을 포함시킬 필요가 없습니다.

#### Qt 모델링 언어 파일 `.qml`

그래픽 QML 애플리케이션은 Qt Widgets 애플리케이션과 관련이 없으며 보통 QML 프로젝트에서 Python 파일이 QML 파일을 로드합니다. 선택적으로 Python에서 정의한 요소가 QML에 노출되는 경우가 있습니다.

손수 `.qml` 파일을 작성할 수도 있고 Qt Creator에 내장된 QML Designer와 같은 도구를 사용할 수도 있습니다. 또, Qt Design Studio와 같은 상업용 도구를 사용할 수도 있습니다.

다음은 `.qml` 파일의 예제입니다. 이 코드는 밝은 회색 직사각형을 표시하고 그 위에 "Hello world!" 메시지를 보여줍니다.

```qml
import QtQuick 2.0

Rectangle {
    id: page
    width: 320;
    height: 480
    color: "lightgray"

    Text {
        id: helloText
        text: "Hello world!"
        y: 30
        anchors.horizontalCenter: page.horizontalCenter
        font.pointSize: 24;
        font.bold: true
    }
}
```

#### Qt Creator Python 프로젝트 파일 `.pyproject`

Qt Creator에서는 Python 기반 프로젝트를 로드하고 처리하기 위한 특별한 파일이 필요합니다.

Qt Creator 예전 버전에서는 `.pyqtc` 확장자를 가진 간단한 포맷을 제공했는데 여기는 다음과 같이 라인마다 파일 이름이 있었습니다:

```
library/server.py
library/client.py
logger.py
...
```

이 포맷에는 제한사항이 있고 추가되는 여러 가지 옵션을 지원하지 않으므로 `.pyproject` 파일이 생겨나게 되었습니다. 이 파일은 JSON-기반 파일이며 더 많은 옵션이 추가될 수 있습니다. 다음은 예제 파일입니다.

```
{
    "files": ["library/server.py", "library/client.py", "logger.py", ...]
}
```

### 애플리케이션을 다른 시스템/플랫폼에 배포하기

애플리케이션을 개발한 후에 다른 사용자들에게 배포하고 싶을 수 있습니다. 당신이 Python 패키지에 대한 경험이 없다고 가정하고 이러한 질문을 드리겠습니다: 어떻게 Python 실행파일을 만들 수 있죠?

만약 당신이 컴파일 프로그래밍 언어 개발자라면 배포라는 게 너무 쉬운 일이겠지만 Python의 경우 조금 어렵습니다.

Python 애플리케이션의 경우 배포 과정을 가리켜 "freezing(동결)"이라고 합니다. 즉, 다른 사용자에게 가상 환경 컨텐츠를 배포하는 것입니다.

#### 재현 가능한 배포

일반적인 접근 방식은 단지 `requirements.txt` 파일을 제공하는 것입니다. 이것은 의존성을 기술한 것입니다. 사용자는 애플리케이션을 실행하기 위해 필요한 것들을 설치해야 합니다.

예를 들어, `main.py`에서 사용하는 2개의 의존성(`module_a`, `module_b`)을 가진 프로젝트를 가지고 있다고 가정해 봅시다. 구조는 다음과 같습니다.

```python
# Content of the main.py file
from module_a import something
import module_b

# ...
```

이 애플리케이션에 대한 `requirements.txt` 파일의 내용은 다음과 같습니다.

```
module_a
module_b
```

나중에 사용자가 `main.py`를 실행하고 싶을 때 새로운 가상 환경에서 `pip install -r requirements.txt`를 이용하여 의존성을 설치해야 합니다.

#### 애플리케이션 동결하기

이 방법은 사용자가 애플리케이션을 배포하는 가장 일반적인 접근 방식입니다. 그리고 이렇게 하면 최종 사용자도 코드를 이용할 수 있습니다만 코드를 가져오는 것은 어려워집니다.

배포 섹션에서 가장 잘 알려진 도구를 기반으로 일련의 튜토리얼을 통해 Python 사용자가 애플리케이션을 동결하고 배포할 수 있게 해줍니다.

#### Python 컴파일하기

비록 Python이 네이티브에서 컴파일하는 것을 지원하지는 않지만 이것을 달성할 수 있도록 해주는 보완 도구가 있습니다. 자세한 것은 [Nuitka](https://nuitka.net/) 프로젝트를 보십시오.

### 왜 Qt for Python인가?

이 질문에 대답하려면 한 발 물러서서 언어에 대한 이야기를 좀 해보아야 합니다.

Python은 Qt와 비슷한 기간 동안 사용되어 왔으며, 성장하고 다방면에서 사용되도록 변화되고, 사용 받고, 여러 프로그래밍 영역에서 필요로 하게 되었습니다.

현재(2021), Python을 논하지 않고는 머신 러닝과 인공지능을 보기 힘들어지게 되었습니다. 이와 마찬가지로 데이터 사이언스/분석/공학 분야에서 대부분 Python과 연관되어 있음을 알 수 있습니다.

지난 해 동안 StackOverflow 설문과 같이 Python 언어의 진화 및 선호도를 보여주는 공개 설문 조사를 통해 이 진술을 검증할 수 있습니다.

| | 2019 | 2020 | 2021 |
| --- | --- | --- | --- |
| 가장 사랑 받는 언어 | 2위 | 3위 | 6위 |
| 가장 원하는 언어 | 1위 | 1위 | 1위 |

[TIOBE 순위](https://www.tiobe.com/tiobe-index/)에서도 볼 수 있습니다.

이러한 자료들이 언어를 판단하기에는 충분하지 않을 수 있지만, 전세계에서 개발자 간의 추세를 확실히 알 수 있습니다.

#### Qt 장벽 낮추기

베테랑 C++ 개발자는 Qt 애플리케이션을 만드는 것이 어렵지 않을 것이며, Qt로 작성된 다른 코드 베이스를 이해하는 데에도 도움이 됩니다. 게다가 많은 팀들이 여러 분야에 걸쳐 있고 다른 팀/회사 개발자가 C++에 능숙하지 않을 수 있습니다.

Python은 사람들을 프로그래밍 세계로 끌어드리고 있으며, 이와 같은 이유로 서로 다른 배경을 가진 사람들이 코드를 작성할 수 있다는 것은 흔한 일입니다. 이는 서로 다른 팀들이 "같은 언어"로 말할 수 있음을 의미합니다.

Python으로 Qt 애플리케이션을 만드는 것은 많은 코드 라인을 필요로 하지 않으며 실행하기 위한 많은 구성을 필요로 하지 않습니다. 다음의 불공정한 예제를 통해 간단한 hello world 애플리케이션 코드를 봅시다.

* C++ 헤더
```cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <QPushButton>

class MainWindow : public QMainWindow
{
    Q_OBJECT
    public:
        MainWindow(QWidget *parent = nullptr);
    private slots:
        void handleButton();
    private:
        QPushButton *m_button;
};

#endif // MAINWINDOW_H
```

* C++ 구현
```cpp
#include "mainwindow.h"

MainWindow::MainWindow(QWidget *parent)
   : QMainWindow(parent)
{
    m_button = new QPushButton("My Button", this);
    connect(m_button, SIGNAL(clicked()), this,
            SLOT(handleButton()));
}

void MainWindow::handleButton()
{
    m_button->setText("Ready");
}
```

* C++ 메인
```cpp
#include <QApplication>
#include "mainwindow.h"

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    MainWindow mainWindow;
    mainWindow.show();
    return app.exec(d);
}
```

* Python
```python
import sys
from pyside6.QtWidgets import (QApplication, QMainWindow,
                               QPushButton)

class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        QMainWindow.__init__(self, parent)
        self.button = QPushButton("My Button", self)
        self.button.clicked.connect(self.handleButton)

    def handleButton(self):
        self.button.setText("Ready")

if __name__ == "__main__":
    app = QApplication([])
    mainWindow = MainWindow()
    mainWindow.show()
    sys.exit(app.exec())
```

대부분의 판에 박힌 코드는 훌륭한 IDE가 제공한다고 말하는 것이 맞습니다만, 외부 도구를 능숙하게 사용하려면 약간의 연습이 필요합니다.

#### 단결이 힘을 만든다

우리의 목표는 더 많은 사용자가 Qt 세계에 들어오게 하는 것입니다. 하지만 이것이 C++ 개발자가 잊혀지게 된다는 것을 의미하지는 않습니다.

바인딩과 함께 Qt for Python이 바인딩 제너레이터 Shiboken을 제공합니다. 비디오 섹션에서 이 기능에 대해 많이 보여 드립니다.

두 언어 간의 바인딩을 생성하는 것은 새로운 것이 아닙니다만, 프로젝트에서 외부 모듈/라이브러리를 사용할 때 최대한 호환되도록 하기 위해 항상 수반되는 사소한 작업이 있습니다.

Shiboken의 주요 사용 사례는 Qt/C++ 프로젝트의 기능을 확장하여 스크립트화 할 수 있도록 하는 것입니다.

애플리케이션이 스크립트화 할 수 있는 것은 무엇을 의미합니까?

* 인터프리트되는 언어가 Qt/C++ 애플리케이션과 직접 상호작용 할 수 있게 함
* Python에서 애플리케이션의 컴포넌트/요소를 수정 및 생성할 수 있는 선택권을 제공함
* 애플리케이션에 대한 플러그인/애드온 시스템을 생성할 수 있는 가능성
* 외부 Python 기능으로 프로세스를 보완함

실습 예제에 대해서는 [Shiboken Webinar](https://www.youtube.com/watch?v=wOMlDutOWXI)를 확인하십시오.

Shiboken은 Qt-종속 바인딩 생성에 탁월합니다. 이는 Qt/C++ 프로젝트가 Python에 쉽게 노출될 수 있음을 의미합니다. 그리고 Shiboken은 이벤트 토크 및 블로그 포스트를 통해 볼 수 있듯이 (Qt 없이) C++ 프로젝트에 대한 지원을 입증했습니다.

잘 알려진 솔루션 프로젝트에 Python 지원이 추가되는 것은 이 산업 분야의 여러 분야의 장치에서 계속 볼 수 있습니다. 이것이 Qt for Python을 제품을 날마다 개선하기 위해 일하는 이유입니다.

Qt와 Python  모두 이러한 상호 작용으로 이익을 얻게 되리라고 믿습니다.

## 시작하기

#### 일반 요구사항

Qt for Python을 빌드하기 전에 다음 필수 구성요소를 설치해야 합니다. Linux의 경우 운영체제 패키지 매니저를 통해 설치할 수 있습니다. macOS의 경우 `brew`를 통해 설치할 수 있습니다. Windows의 경우 각 웹사이트에서 인스톨러를 다운로드할 수 있습니다.

* Python: 3.7 이상 [공식 Python 웹사이트](https://www.python.org/downloads/)
* Qt: 6.4 이상 [온라인 인스톨러](https://download.qt.io/official_releases/online_installers/)
* CMake: 3.18 이상 [공식 CMake 웹사이트](https://cmake.org/download/)
* Git: 2.0 이상 [공식 Git 웹사이트](https://git-scm.com/downloads)
* libclang: libclang 라이브러리는 Qt 6.0 이상의 경우 버전 10을 권장함. 각 OS에 대한 미리 빌드된 버전의 경우 [여기](https://download.qt.io/development_releases/prebuilt/libclang/)에서 다운로드 할 수 있음
* [Qt가 지원되는 플랫폼](https://doc.qt.io/qt-6/supported-platforms.html)을 확인하십시오.

#### 플랫폼 별 가이드

자세한 내용은 생략한다.

### 패키지 세부사항

설치 커맨드 한 줄이면 Qt 프레임워크와 같은 큰 프로젝트를 사용할 수 있습니다:

`pip install pyside6`

매우 유용하지만 초심자들에게는 당혹스러울 수 있습니다.

IDE 외에도 Qt 애플리케이션을 개발하기 위해 다른 어떤 것도 설치할 필요가 없습니다. 왜냐하면 이 한 줄로 UI 설계, QML 타입 사용, 파일 자동 생성, 애플리케이션 번역 등 많은 도구들이 설치되기 때문입니다.

#### 패키지 의존성

6.3.0부터 `pyside6` 패키지는 거의 비어 있으며 모든 모듈을 제대로 사용하기 위해 필요한 다른 패키지에 대한 참조만을 포함하고 있습니다. 이 패키지는 다음과 같습니다.

* `pyside6-essentials`, [필수 Qt 모듈](https://pypi.org/project/PySide6-Essentials/)
* `pyside6-addons`, [추가 Qt 모듈](https://pypi.org/project/PySide6-Addons/)
* `shiboken6`, 유틸리티 Python 모듈

`pip list`를 실행해서 Python (가상) 환경에 설치된 패키지를 확인할 수 있습니다.

`pyside6-essentials`와 `pyside6-addons`는 Qt 바이너리(`.so`, `.dll` 또는 `.dylib`)를 가지고 있는데 Python에서 Qt 모듈을 사용할 수 있도록 해주는 Python 랩퍼가 이것들을 사용합니다. 예를 들어 Linux 플랫폼의 경우 `QtCore` 모듈에서 다음을 찾을 수 있습니다.

* `PySide6/QtCore.abi3.so`, 그리고
* `PySide6/Qt/lib/libQt6Core.so.6`

당신의 (가상) 환경의 `site-packages` 디렉토리 안에서 찾을 수 있습니다. 1번째는 importable 모듈이고 이것은 원래 QtCore 라이브러리인 2번째 파일에 의존하고 있습니다.

#### 포함된 도구

패키지 안에도 `uic`, `rcc` 등 Qt 애플리케이션 개발 워크플로우에서 중요한 Qt 도구들이 포함되어 있습니다.

모든 도구는 반드시 PySide 랩퍼를 통해 사용되어야 하고 직접 사용해서는 안 됩니다. 예를 들어, 설치된 `site-packages/` 디렉토리를 탐색하려면 (Windows의 경우) `uic.exe`를 찾고 싶으면 그것을 클릭하지 말고 `pyside6-uic.exe`를 대신 사용해야 합니다. 이렇게 하는 이유는 설치된 Python 패키지로 적절하게 작업하려면 PATH, 플러그인 등의 적절한 설정을 하기 위해서입니다.

다음은 주제 별로 그룹화된 버전 6.3.0부터 Qt for Python에 포함된 도구들입니다.

##### 프로젝트 개발

* `pyside6-project`: `.pyproject` 파일에 있는 Qt Designer 폼(`.ui` 파일), 리소스 파일(`.qrc), QML 타입 파일(`.qmltype)을 빌드함.

##### 위젯 개발

* `pyside6-designer`: 위젯 UI를 설계하기 위한 드래그-앤-드롭 도구. (`.ui` 파일 생성)
* `pyside6-uic`: `.ui` 폼 파일로부터 Python 코드 생성함.
* `pyside6-rcc`: `.qrc` 리소스 파일로부터 직렬화된 데이터 생성함. 이 파일들은 다른 비-위젯 프로젝트에서 사용할 수 있음을 명심하십시오.

##### QML 개발

* `pyside6-qmllint`: QML 파일의 구문 유효성을 검증함.
* `pyside6-qmltyperegistrar`: 메타 타입을 읽고 관련 매크로와 함께 표시된 모든 타입을 등록하기 위해 필요한 코드를 포함하는 파일을 생성함.
* `pyside6-qmlimportscanner`: 프로젝트/QML 파일로부터 가져온 QML 모듈을 식별하고 JSON 배열로 결과를 덤프함.
* `pyside6-qmlcachegen`: 바이너리로 번들링하기 위해 컴파일 시간에 QML을 바이트코드로 컴파일함.
* `pyside6-qmlsc`: `pyside6-qmlcachegen`을 대체함. 이 도구는 QML을 바이트코드로 컴파일할뿐만 아니라 철저한 분석을 위해 C++ 코드도 생성함. 이것은 상업용 전용 도구임.

##### 번역

* `pyside6-linguist`: 애플리케이션에서 텍스트를 번역하기 위함.
* `pyside6-lrelease`: 애플리케이션을 위한 런타임 번역 파일을 생성함.
* `pyside6-lupdate`: 소스 파일과 번역 파일을 동기화함.

##### Qt 도움말

* `pyside6-assistant`: Qt Help 파일 포맷으로 된 온라인 문서를 보기 위함. 이 포맷에 대한 자세한 것은 [QtHelp Framework](https://doc.qt.io/qt-6/qthelp-framework.html) 페이지를 보십시오.

##### PySide 유틸리티

* `pyside6-genpyi`: Qt 모듈을 위한 Python 스텁(`.pyi` 파일)을 생성함
* `pyside6-metaobjectdump`: `qmltyperegistrar`의 입력으로 사용될 JSON 형식의 메타타입 정보를 출력하기 위한 도구.

##### 배포

* `pyside6-deploy`: PySide6 애플리케이션을 데스크톱 플랫폼(Linux, Windows, macOS)에 배포하기 위함.
* `pyside6-android-deploy`: PySide6 애플리케이션을 Android 플랫폼(aarch64, armv7a, i686, x86_64)에 배포하기 위함.

## 모듈 API

#### 기본 모듈

위젯 기반 UI를 만드는 데 도움을 주는 메인 모듈은 다음과 같습니다.

* [QtCore](https://doc.qt.io/qtforpython-6/PySide6/QtCore/index.html#module-PySide6.QtCore): 시그널, 슬롯, 프로퍼티, 아이템 모델의 베이스 클래스, 직렬화 등 코어 비-GUI 기능을 제공함.

* [QtGui](https://doc.qt.io/qtforpython-6/PySide6/QtGui/index.html#module-PySide6.QtGui): QtCore의 GUI 기능을 확장함: 이벤트, 윈도우, 스크린, OpenGL, 래스터-기반 2D 그리기, 이미지.

* [QtWidgets](https://doc.qt.io/qtforpython-6/PySide6/QtWidgets/index.html#module-PySide6.QtWidgets): 애플리케이션에 위젯을 사용할 수 있도록 함. UI에 그래픽 요소를 포함시킴.

#### QML과 Qt Quick

Python에서 QML 언어로 상호작용하기 위해 다음 모듈을 사용하십시오.

* [QtQml](https://doc.qt.io/qtforpython-6/PySide6/QtQml/index.html#module-PySide6.QtQml): 모듈과 상호작용하기 위한 베이스 Python API.

* [QtQuick](https://doc.qt.io/qtforpython-6/PySide6/QtQuick/index.html#module-PySide6.QtQuick): Qt 애플리케이션에 Qt Quick을 내장시키기 위한 클래스를 제공함.

* [QtQuickWidgets](https://doc.qt.io/qtforpython-6/PySide6/QtQuickWidgets/index.html#module-PySide6.QtQuickWidgets): 위젯-기반 애플리케이션에 Qt Quick을 내장시키기 위한 QQuickWidget 클래스를 제공함.

### Qt for Python이 지원하는 Qt 모듈

* [QtBluetooth](https://doc.qt.io/qtforpython-6/PySide6/QtBluetooth/index.html#module-PySide6.QtBluetooth): Bluetooth API는 Bluetooth 가능한 장비 간의 연결을 제공함.

* [QtCharts](https://doc.qt.io/qtforpython-6/PySide6/QtCharts/index.html#module-PySide6.QtCharts): 차트 컴포넌트를 쉽게 사용할 수 있는 집합을 제공함.

* [QtConcurrent](https://doc.qt.io/qtforpython-6/PySide6/QtConcurrent/index.html#module-PySide6.QtConcurrent): 저수준 쓰레드 프리미티브(뮤텍스, 읽기-쓰기 락, 대기 조건, 세마포어)를 사용하지 않고도 멀티-쓰레드 프로그램을 작성할 수 있는 고수준 API를 제공함.

* [QtCore](https://doc.qt.io/qtforpython-6/PySide6/QtCore/index.html#module-PySide6.QtCore): 코어 비-GUI 기능을 제공함.

* [QtDataVisualization](https://doc.qt.io/qtforpython-6/PySide6/QtDataVisualization/index.html#module-PySide6.QtDataVisualization): 데이터를 3D 막대, 산포도, 면 그래프로 시각화 할 수 있는 방법을 제공함.

* [QtDBus](https://doc.qt.io/qtforpython-6/PySide6/QtDBus/index.html#module-PySide6.QtDBus): D-Bus는 기존의 경쟁 IPC 솔루션을 하나의 통합 프로토콜로 대체하기 위해 Linux용으로 개발된 프로세스간 통신(IPC) 및 원격 프로시저 호출(RPC) 메커니즘입니다.

* [QtDesigner](https://doc.qt.io/qtforpython-6/PySide6/QtDesigner/index.html#module-PySide6.QtDesigner): Qt Designer를 확장하기 위한 클래스를 제공함.

* [QtGui](https://doc.qt.io/qtforpython-6/PySide6/QtGui/index.html#module-PySide6.QtGui): QtCore의 GUI 기능을 확장함.

* [QtHelp](https://doc.qt.io/qtforpython-6/PySide6/QtHelp/index.html#module-PySide6.QtHelp): 애플리케이션의 온라인 문서를 통합하기 위한 클래스를 제공함.

* [Qt Multimedia](https://doc.qt.io/qtforpython-6/PySide6/QtMultimedia/index.html#module-PySide6.QtMultimedia): 멀티미디어 특화 사용자 사례에 대한 API를 제공함.

* [Qt Multimedia Widgets](https://doc.qt.io/qtforpython-6/PySide6/QtMultimediaWidgets/index.html#module-PySide6.QtMultimediaWidgets): 위젯 기반 멀티미디어 API를 제공함.

* [QtNetwork](https://doc.qt.io/qtforpython-6/PySide6/QtNetwork/index.html#module-PySide6.QtNetwork): TCP/IP 클라이언트 및 서버를 작성할 수 있도록 하는 클래스를 제공함.

* [Qt Network Authorization](https://doc.qt.io/qtforpython-6/PySide6/QtNetworkAuth/index.html#module-PySide6.QtNetworkAuth): Qt 애플리케이션이 사용자의 비밀번호를 노출하지 않고 온라인 계정 및 HTTP 서비스에 제한적으로 접근할 수 있도록 하는 API 집합을 제공함.

* [QtNfc](https://doc.qt.io/qtforpython-6/PySide6/QtNfc/index.html#module-PySide6.QtNfc): NFC API는 NFC 가능한 장치 간의 연결을 제공함.

* [QtOpenGL](https://doc.qt.io/qtforpython-6/PySide6/QtOpenGL/index.html#module-PySide6.QtOpenGL): Qt 애플리케이션에서 OpenGL을 쉽게 사용할 수 있도록 하는 클래스를 제공함.

* [QtOpenGL Widgets](https://doc.qt.io/qtforpython-6/PySide6/QtOpenGLWidgets/index.html#module-PySide6.QtOpenGLWidgets): 위젯 트리의 특정 부분에 대해 OpenGL 렌더링을 활성화하는 OpenGLWidget 클래스를 제공함.

* [Qt Positioning](https://doc.qt.io/qtforpython-6/PySide6/QtPositioning/index.html#module-PySide6.QtPositioning): 위치, 위성 정보, 지역 모니터링 클래스에 대한 접근을 제공함.

* [Qt PDF](https://doc.qt.io/qtforpython-6/PySide6/QtPdf/index.html#module-PySide6.QtPdf): PDF 문서를 렌더링하기 위한 클래스와 함수.

* [Qt PDF Widgets](https://doc.qt.io/qtforpython-6/PySide6/QtPdfWidgets/index.html#module-PySide6.QtPdfWidgets): PDF 뷰어 위젯.

* [QtPrintSupport](https://doc.qt.io/qtforpython-6/PySide6/QtPrintSupport/index.html#module-PySide6.QtPrintSupport): 인쇄를 위한 광범위한 크로스-플랫폼 지원을 제공함.

* [QtQml](https://doc.qt.io/qtforpython-6/PySide6/QtQml/index.html#module-PySide6.QtQml): Qt QML을 위한 Python API.

* [QtQuick](https://doc.qt.io/qtforpython-6/PySide6/QtQuick/index.html#module-PySide6.QtQuick): Qt 애플리케이션에 Qt Quick을 내장시키기 위한 클래스를 제공함.

* [QtQuickControls2](https://doc.qt.io/qtforpython-6/PySide6/QtQuickControls2/index.html#module-PySide6.QtQuickControls2): C++의 컨트롤을 설정하기 위한 클래스를 제공함.

* [QtQuickWidgets](https://doc.qt.io/qtforpython-6/PySide6/QtQuickWidgets/index.html#module-PySide6.QtQuickWidgets): 위젯 기반 애플리케이션에 Qt Quick을 내장시키기 위한 QQuickWidget 클래스를 제공함.

* [QtRemoteObjects](https://doc.qt.io/qtforpython-6/PySide6/QtRemoteObjects/index.html#module-PySide6.QtRemoteObjects): Qt를 위해 개발된 프로세스간 통신(IPC) 모듈. 이 모듈은 Qt의 기존 기능을 확장하여 프로세스 또는 컴퓨터 간의 정보 교환을 쉽게 할 수 있게 해줍니다.

* [Qt Scxml](https://doc.qt.io/qtforpython-6/PySide6/QtScxml/index.html#module-PySide6.QtScxml): SCXML 파일로부터 상태 머신을 생성 및 사용할 수 있게 해주는 클래스를 제공함.

* [Qt Sensors](https://doc.qt.io/qtforpython-6/PySide6/QtSensors/index.html#module-PySide6.QtSensors): 센서 하드웨어에 대한 접근을 제공함.

* [Qt Serial Bus](https://doc.qt.io/qtforpython-6/PySide6/QtSerialBus/index.html#module-PySide6.QtSerialBus): 시리얼 산업 버스 인터페이스에 대한 접근을 제공함. 현재 이 모듈은 CAN 버스와 Modbus 프로토콜을 지원함.

* [Qt Serial Port](https://doc.qt.io/qtforpython-6/PySide6/QtSerialPort/index.html#module-PySide6.QtSerialPort): 하드웨어 및 가상 시리얼 포트와 상호작용하기 위한 클래스를 제공함.

* [Qt Spatial Audio](https://doc.qt.io/qtforpython-6/PySide6/QtSpatialAudio/index.html#module-PySide6.QtSpatialAudio): 음원 및 서라운드를 3D 공간에 모델링하기 위한 API를 제공함.

* [QtSql](https://doc.qt.io/qtforpython-6/PySide6/QtSql/index.html#module-PySide6.QtSql): Qt 애플리케이션에 원활한 데이터베이스 통합을 제공함.

* [QtStateMachine](https://doc.qt.io/qtforpython-6/PySide6/QtStateMachine/index.html#module-PySide6.QtStateMachine): 상태 그래프를 생성 및 실행하기 위한 클래스를 제공함.

* [QtSvg](https://doc.qt.io/qtforpython-6/PySide6/QtSvg/index.html#module-PySide6.QtSvg): SVG 파일의 내용을 표시하기 위한 클래스를 제공함.

* [QtSvgWidgets](https://doc.qt.io/qtforpython-6/PySide6/QtSvgWidgets/index.html#module-PySide6.QtSvgWidgets): SVG 파일의 내용을 표시하는 데 사용하는 위젯을 제공함.

* [QtTest](https://doc.qt.io/qtforpython-6/PySide6/QtTest/index.html#module-PySide6.QtTest): Qt 애플리케이션 및 라이브러리를 단위 테스트하기 위한 클래스를 제공함.

* [QtUiTools](https://doc.qt.io/qtforpython-6/PySide6/QtUiTools/index.html#module-PySide6.QtUiTools): Qt Designer로 생성된 폼을 처리하기 위한 클래스를 제공함.

* [Qt WebChannel](https://doc.qt.io/qtforpython-6/PySide6/QtWebChannel/index.html#module-PySide6.QtWebChannel): Qt 애플리케이션을 HTML/JavaScript 클라이언트와 원활하게 통합하기 위해 HTML 클라이언트로부터 QObject 또는 QML 객체에 대한 접근을 제공함.

* [QtWebEngine Core C++ Classes](https://doc.qt.io/qtforpython-6/PySide6/QtWebEngineCore/index.html#module-PySide6.QtWebEngineCore): QtWebEngine과 QtWebEngineWidgets이 공유하는 공용 API를 제공함.

* [QtWebEngine Widgets C++ Classes](https://doc.qt.io/qtforpython-6/PySide6/QtWebEngineWidgets/index.html#module-PySide6.QtWebEngineWidgets): QWidget 기반 애플리케이션에서 웹 컨텐츠를 렌더링하기 위한 C++ 클래스를 제공함.

* [QtWebEngine QML Types](https://doc.qt.io/qtforpython-6/PySide6/QtWebEngineQuick/index.html#module-PySide6.QtWebEngineQuick): QML 애플리케이션에서 웹 컨텐츠를 렌더링하기 위한 QML 타입을 제공함.

* [Qt WebSockets](https://doc.qt.io/qtforpython-6/PySide6/QtWebSockets/index.html#module-PySide6.QtWebSockets): RFC 6455를 준수하는 WebSocket 통신을 제공함.

* [QtWidgets](https://doc.qt.io/qtforpython-6/PySide6/QtWidgets/index.html#module-PySide6.QtWidgets): Qt GUI의 C++ 위젯 기능을 확장함.

* [QtXml](https://doc.qt.io/qtforpython-6/PySide6/QtXml/index.html#module-PySide6.QtXml): DOM의 C++ 구현을 제공함.

* [Qt3DAnimation](https://doc.qt.io/qtforpython-6/PySide6/Qt3DAnimation/index.html#module-PySide6.Qt3DAnimation): 3D 객체를 애니메이션화 하는 데 필요한 기본 요소를 제공함.

* [Qt3D Core](https://doc.qt.io/qtforpython-6/PySide6/Qt3DCore/index.html#module-PySide6.Qt3DCore): 근실시간 시뮬레이션 시스템을 지원하기 위한 기능을 포함하고 있음.

* [Qt3D Extras](https://doc.qt.io/qtforpython-6/PySide6/Qt3DExtras/index.html#module-PySide6.Qt3DExtras): Qt 3D를 시작하는 데 도움을 주는 미리 빌드된 요소 집합을 제공함.

* [Qt3D Input](https://doc.qt.io/qtforpython-6/PySide6/Qt3DInput/index.html#module-PySide6.Qt3DInput): Qt 3D를 사용하는 애플리케이션에서 사용자 입력을 처리하기 위한 클래스를 제공함.

* [Qt3D Logic](https://doc.qt.io/qtforpython-6/PySide6/Qt3DLogic/index.html#module-PySide6.Qt3DLogic): 프레임을 Qt 3D 백엔드와 동기화할 수 있게 해줌.

* [Qt3D Render](https://doc.qt.io/qtforpython-6/PySide6/Qt3DRender/index.html#module-PySide6.Qt3DRender): Qt 3D를 사용하여 2D 및 3D 렌더링을 지원하는 기능이 포함되어 있음.

* [Qt CoAP](https://doc.qt.io/qtforpython-6/PySide6/QtCoap/index.html#module-PySide6.QtCoap): RFC 7252에서 정의한 CoAP의 클라이언트 사이드를 구현함.

* [Qt OPC UA](https://doc.qt.io/qtforpython-6/PySide6/QtOpcUa/index.html#module-PySide6.QtOpcUa): 산업 애플리케이션에서 데이터 모델링 및 데이터 교환을 위한 프로토콜.

* [Qt MQTT](https://doc.qt.io/qtforpython-6/PySide6/QtMqtt/index.html#module-PySide6.QtMqtt): MQTT 프로토콜 사양의 구현을 제공함.
