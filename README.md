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

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/6316420d-e3ee-4449-93c2-03088ad0e91e)

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

## 튜토리얼

### 처음 QtWidgets 애플리케이션

```python
import sys
from PySide6.QtWidgets import QApplication, QLabel

app = QApplication(sys.argv)      # app = QApplication([]) --> 커맨드 라인에서 인수를 안 받을 경우 이렇게 해도 됨
label = QLabel("Hello World!")    # label = QLabel("<font color=red size=40>Hello World!</font>")  --> 이런 식으로 HTML 코드를 넣을 수도 있음
label.show()
app.exec()
```

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/0f64a820-9e07-4239-b473-68811686f487)


### 간단한 Button 사용하기

여기서는 시그널과 슬롯 개념이 나옵니다.
시그널은 '클릭'과 같은 이벤트를 의미합니다.
슬롯 함수는 시그널(이벤트)와 연결된 함수로 시그널이 발생하면 호출되는 함수를 의미합니다. @Slot() 데코레이터를 위에 붙여 주도록 합시다.

```python
#!/usr/bin/python

import sys
from PySide6.QtWidgets import QApplication, QPushButton
from PySide6.QtCore import Slot

@Slot()
def say_hello():
  print("Button clicked, Hello!")    # 콘솔 창에 메시지 출력

app = QApplication(sys.argv)
button = QPushButton("Click me")
button.clicked.connect(say_hello)    # clicked 시그널과 say_hello 함수를 연결함
button.show()
app.exec()
```

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/3d41165d-b2ee-46ad-b463-b6b9e7354136)

### 시그널과 슬롯

시그널과 슬롯은 `QObject` 간의 통신 메커니즘이며 Qt의 핵심 기능입니다. 시그널과 슬롯은 마치 스위치(시그널)와 전등(슬롯)과 같다고 볼 수 있습니다.

`QWidget`과 같이 `QObject`에서 파생된 모든 클래스는 시그널과 슬롯을 갖고 있습니다. 객체의 상태가 바뀌면 다른 객체가 알아들을 수 있는 시그널이 방출됩니다. 어떤 객체가 시그널을 방출했는지는 알 수 없기 때문에 진정한 정보 캡슐화가 이루어진다고 볼 수 있습니다. 그리고 객체는 하나의 소프트웨어 컴포넌트 역할을 할 수 있습니다.

시그널을 수신하는 슬롯은 일반 멤버 함수이기도 합니다. 객체는 누가 시그널을 받는지 알 수 없고, 슬롯 함수는 시그널이 어디에 연결되어 있는지 알 수 없습니다.

슬롯 하나에 여러 시그널이 연결될 수도 있고, 시그널 하나에 여러 슬롯이 연결될 수도 있습니다. 심지어 시그널과 다른 시그널이 연결될 수도 있습니다. (이렇게 하면 1번째 시그널이 방출되면 2번째 시그널도 즉시 방출됨)

Qt의 위젯은 미리 정의된 시그널, 슬롯을 많이 갖고 있습니다. 예를 들어 (버튼의 베이스 클래스인) `QAbstractButton`은 `clicked()` 시그널을 갖고 있고, (단일 라인 입력 필드인) `QLineEdit`는 `clear()`라는 슬롯을 갖고 있습니다. 그래서 텍스트를 지우기 위해 버튼이 딸린 텍스트 입력 필드를 구현하고자 할 때, `QLineEdit` 오른쪽에 `QToolButton`을 배치하고 `clicked()` 시그널과 `clear()` 슬롯을 연결하면 됩니다. 시그널의 `connect()` 메서드를 사용하면 됩니다.

```python
button = QToolButton()
line_edit = QLineEdit()
button.clicked.connect(line_edit.clear)
```

`connect()` 메서드는 `QMetaObject.Connection` 객체를 리턴합니다. 이 객체는 연결을 끊기 위해 `disconnect()` 메서드와 함께 사용될 수 있습니다.

시그널은 일반 함수에도 연결될 수 있습니다:

```python
import sys
from PySide6.QtWidgets import QApplication, QPushButton

def function():
    print("The 'function' has been called!")

app = QApplication()
button = QPushButton("Call function")
button.clicked.connect(function)    # 시그널이 일반 함수와 연결됨
button.show()
sys.exit(app.exec())
```

코드 형태로 연결을 할 수도 있고, Qt Designer의 Signal-Slot Editor에서 설계된 위젯 폼에서도 연결할 수 있습니다.

#### 시그널 클래스

Python에서 클래스를 작성할 때, 시그널은 클래스 `QtCore.Signal()`의 클래스 레벨 변수로 선언됩니다. QWidget 기반 버튼은 다음과 같이 `clicked()` 시그널을 방출합니다.

```python
from PySide6.QtCore import Qt, Signal
from PySide6.QtWidgets import QWidget

class Button(QWidget):

    clicked = Signal(Qt.MouseButton)     # clicked 시그널 선언

    ...

    def mousePressEvent(self, event):    # 마우스 버튼을 누르는 이벤트가 발생하면
        self.clicked.emit(event.button())  # 클릭 시그널 방출
```

`Signal`의 생성자는 Python 타입 또는 C 타입의 튜플 또는 리스트를 인수로 받습니다:

```python
signal1 = Signal(int)                 # Python 타입
signal2 = Signal(QUrl)                # Qt 타입
signal3 = Signal(int, str, int)       # 다수의 타입
signal4 = Signal((float,), (QDate,))  # 선택적인 타입
```

그 외에도 시그널 이름을 정의하는 네임드 인수 `name`도 받을 수 있습니다. 만약 아무 것도 전달되지 않으면, 새로운 시그널은 할당될 변수와 같은 이름을 갖게 될 것입니다.

```python
# TODO
signal5 = Signal(int, name='rangeChanged')
# ...
rangeChanged.emit(...)
```

`Signal`의 또 다른 유용한 옵션은 인수 이름이며, QML 애플리케이션에서 방출된 값을 이름으로 참조하는 데 유용합니다:

```python
sumResult = Signal(int, arguments=['sum'])
```

```python
Connections {
    target: ...
    function onSumResult(sum) {
        // do something with 'sum'
    }
```

#### 슬롯 클래스

QObject 파생 클래스에 있는 슬롯은 데코레이터 `@QtCore.Slot()`로 표시해야 합니다. 또, 시그네처를 정의하려면 `QtCore.Signal()` 클래스와 마찬가지로 타입을 넘겨주기만 하면 됩니다.

```python
@Slot(str)
def slot_function(self, s):
    ...
```

또 `Slot()`은 `name`과 `result` 키워드를 받아들입니다. `result` 키워드는 리턴될 타입을 정의하며 C 또는 Python 타입이 될 수 있습니다. `name` 키워드는 `Signal()`과 같은 방식으로 행동합니다. 만약 전달되는 이름이 없으면 새로운 슬롯은 데코레이트되는 함수와 같은 이름을 갖게 됩니다.

#### 시그널과 슬롯을 다른 타입으로 오버로드하기

다른 파라미터 타입 리스트를 가진 동일한 이름의 시그널과 슬롯을 사용할 수 있습니다. 이것은 Qt 5의 레거시이며 새로운 코드에 권장하지 않습니다. Qt 6에서는 시그널이 다른 타입에 대해 고유한 이름을 갖습니다.

다음 예제에서는 시그널과 슬롯에 2개의 핸들러를 사용하여 서로 다른 기능을 보여 줍니다.

```python
import sys
from PySide6.QtWidgets import QApplication, QPushButton
from PySide6.QtCore import QObject, Signal, Slot

class Communicate(QObject):
    # 즉시 2개의 새 시그널을 만듭니다: 하나는 int 타입, 다른 하나는 string 타입을 처리함
    speak = Signal((int,), (str,))

    def __init__(self, parent=None):
        super().__init__(parent)

        self.speak[int].connect(self.say_something)
        self.speak[str].connect(self.say_something)

    # C 'int' 또는 'str'을 수신하고 'say_something'이라는 이름을 갖는 새로운 슬롯을 정의함
    @Slot(int)
    @Slot(str)
    def say_something(self, arg):
        if isinstance(arg, int):
            print("This is a number:", arg)
        elif isinstance(arg, str):
            print("This is a string:", arg)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    someone = Communicate()

    # 서로 다른 인수를 갖는 'speak' 시그널을 방출함
    someone.speak.emit(10)
    someone.speak[str].emit("Hello everybody!")
```

#### 메서드 시그네처 문자열로 시그널과 슬롯 지정하기

시그널과 슬롯은 `SIGNAL()`과 `SLOT()` 함수를 통해 전달되는 C++ 메서드 시그네처 문자열로 지정할 수도 있습니다:

```python
from PySide6.QtCore import SIGNAL, SLOT

button.connect(SIGNAL("clicked(Qt::MouseButton)"),
              action_handler, SLOT("action1(Qt::MouseButton)"))
```

이것은 시그널을 연결하는 데 권장하지 않으며, 주로 `QWizardPage::registerField()`와 같은 메서드로 시그널을 지정하는 데 사용합니다:

```python
wizard.registerField("text", line_edit, "text",
                     SIGNAL("textChanged(QString)"))
```

### 다이얼로그 애플리케이션 만들기

이 튜토리얼은 기본 위젯 몇 가지를 이용해 간단한 다이얼로그를 만드는 방법을 보여줍니다. 사용자가 `QLineEdit`에 이름을 입력하고 `QPushButton`을 클릭하면 다이얼로그가 환영 인사를 합니다.

```python
import sys
from PySide6.QtWidgets import (QLineEdit, QPushButton, QApplication, QVBoxLayout, QDialog)

# QDialog에서 파생된 Form 클래스 선언
class Form(QDialog):
    # 생성자
    def __init__(self, parent=None):
        super(Form, self).__init__(parent)  # 부모 객체의 생성자 호출
        self.setWindowTitle("My Form")
        # 위젯 생성
        self.edit = QLineEdit("Write my name here")
        self.button = QPushButton("Show Greetings")
        # 레이아웃을 생성하고 위젯을 추가함
        layout = QVBoxLayout()
        layout.addWidget(self.edit)
        layout.addWidget(self.button)
        # 다이얼로그 레이아웃 설정
        self.setLayout(layout)
        # greetings 슬롯에 버튼 시그널 추가함
        self.button.clicked.connect(self.greetings)

    # 사용자 환영 인사
    def greetings(self):
        print(f"Hello {self.edit.text()}")

if __name__ == '__main__':
    # Qt 애플리케이션 생성함
    app = QApplication(sys.argv)
    # 폼을 생성하고 보여줌
    form = Form()
    form.show()
    # 메인 Qt 루프 실행하기
    sys.exit(app.exec())
```

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/ae542707-e187-4363-894f-21263e662773)

### Table 위젯을 사용하여 데이터 표시하기

표에 정렬된 데이터를 표시하고 싶으면 `QTableWidget`을 사용하십시오.

`QTableWidget`을 사용하는 것만이 표에 정보를 표시하는 유일한 경로는 아닙니다. `QTableView`를 이용하여 데이터 모델을 만들고 표시할 수도 있지만 이 튜토리얼의 범위를 벗어납니다.

```python
import sys
from PySide6.QtGui import QColor
from PySide6.QtWidgets import (QApplication, QTableWidget, QTableWidgetItem)

# 컬러의 이름과 HEX 코드 리스트를 포함하는 데이터 모델
colors = [("Red", "#FF0000"),
          ("Green", "#00FF00"),
          ("Blue", "#0000FF"),
          ("Black", "#000000"),
          ("White", "#FFFFFF"),
          ("Electric Green", "#41CD52"),
          ("Dark Blue", "#222840"),
          ("Yellow", "#F9E56d")]

# HEX 코드를 RGB로 변환하는 함수
def get_rgb_from_hex(code):
    code_hex = code.replace("#", "")
    rgb = tuple(int(code_hex[i:i+2], 16) for i in (0, 2, 4))
    return QColor.fromRgb(rgb[0], rgb[1], rgb[2])

app = QApplication()

table = QTableWidget()
table.setRowCount(len(colors))            # 표의 행 개수 = color 변수의 항목 개수
table.setColumnCount(len(colors[0]) + 1)  # 표의 열 개수 = color 변수의 필드 개수 + 1 (Color 탭이 추가됨)
table.setHorizontalHeaderLabels(["Name", "Hex Code", "Color"])    # 표의 헤더 라벨 설정

# 표에 항목을 추가함
for i, (name, code) in enumerate(colors):
    item_name = QTableWidgetItem(name)
    item_code = QTableWidgetItem(code)
    item_color = QTableWidgetItem()
    item_color.setBackground(get_rgb_from_hex(code))
    table.setItem(i, 0, item_name)
    table.setItem(i, 1, item_code)
    table.setItem(i, 2, item_color)

table.show()
sys.exit(app.exec())
```

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/ca3d3d2f-6a58-4aad-9f1c-897d0dec9cf9)

### Tree 위젯을 사용하여 데이터 표시하기

트리에 정렬된 데이터를 표시하고 싶으면 `QTreeWidget`을 사용하십시오.

`QTreeWidget`을 사용하는 것만이 트리에 정보를 표시하는 유일한 경로는 아닙니다. `QTreeView`를 이용하여 데이터 모델을 만들고 표시할 수도 있지만 이 튜토리얼의 범위를 벗어납니다.

```python
import sys
from PySide6.QtWidgets import QApplication, QTreeWidget, QTreeWidgetItem

# 딕셔너리를 정의함, 그리고 각 프로젝트에 속한 파일의 이름을 리스트에 추가함
data = {"Project A": ["file_a.py", "file_a.txt", "something.xls"],
        "Project B": ["file_b.csv", "photo.jpg"],
        "Project C": []}

app = QApplication()

tree = QTreeWidget()
tree.setColumnCount(2)    # 2개의 열 (항목의 이름, 프로젝트 디렉토리의 파일 타입)
tree.setHeaderLabels(["Name", "Type"])    # 트리의 헤더 라벨 설정

items = []
for key, values in data.items():
    item = QTreeWidgetItem([key])
    for value in values:
        ext = value.split(".")[-1].upper()     # 파일 확장자 추출
        child = QTreeWidgetItem([value, ext])  # 파일 이름과 파일 확장자를 자식 항목으로 만듦
        item.addChild(child)
    items.append(item)

tree.insertTopLevelItems(0, items)

tree.show()
sys.exit(app.exec())
```

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/5d85ffde-46b9-47c4-b023-49e682583f57)

### Designer의 `.ui` 파일 사용하기 또는 `QUiLoader` 및 `pyside6-uic`와 함께 QtCreator 사용하기

이 페이지는 Qt for Python 프로젝트를 위해 Qt Widgets 기반 그래픽 인터페이스를 만들기 위한 [Qt Designer](https://doc.qt.io/qt-6/qtdesigner-manual.html) 사용법을 설명합니다. Qt Designer는 그래픽 UI 설계 도구로서 독립형 바이너리(pyside6-designer)로 사용할 수도 있고 [Qt Creator IDE](https://doc.qt.io/qtcreator)에 포함시킬 수도 있습니다. Qt Creator 안에서 사용하는 방법은 [Qt Designer 사용하기](https://doc.qt.io/qtcreator/creator-using-qt-designer.html)에 나와 있습니다.

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/b66cccd2-e27c-4820-8ce6-36a1edcca096)

설계한 내용은 XML 기반 포맷인 `.ui` 파일에 저장됩니다. 이 파일은 [pyside6-uic](https://doc.qt.io/qt-6/uic.html) 도구에 의해 프로젝트 빌드 시간 동안 위젯 인스턴스를 붙이는 Python 또는 C++ 코드로 변환됩니다.

Qt Creator에서 새로운 Qt Design Form을 만들려면, `File/New File Or Project`를 선택하고 "Main Window" 템플릿을 선택하십시오. `mainwindow.ui`로 저장하십시오. 중앙 위젯의 중심에 `QPushButton`을 추가하십시오.

당신이 저장한 `mainwindow.ui` 파일은 다음과 같이 나올 것입니다:

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
   <widget class="QPushButton" name="pushButton">
    <property name="geometry">
     <rect>
      <x>110</x>
      <y>80</y>
      <width>201</width>
      <height>81</height>
     </rect>
    </property>
    <property name="text">
     <string>PushButton</string>
    </property>
   </widget>
  </widget>
  <widget class="QMenuBar" name="menuBar">
   <property name="geometry">
    <rect>
     <x>0</x>
     <y>0</y>
     <width>400</width>
     <height>20</height>
    </rect>
   </property>
  </widget>
  <widget class="QToolBar" name="mainToolBar">
   <attribute name="toolBarArea">
    <enum>TopToolBarArea</enum>
   </attribute>
   <attribute name="toolBarBreak">
    <bool>false</bool>
   </attribute>
  </widget>
  <widget class="QStatusBar" name="statusBar"/>
 </widget>
 <layoutdefault spacing="6" margin="11"/>
 <resources/>
 <connections/>
</ui>
```

이제 Python에서 UI 파일을 사용하는 방법을 선택할 준비가 되었습니다.

#### 선택 A: Python 클래스 생성하기

UI 파일과 상호작용할 수 있는 표준 방법은 UI 파일로부터 Python 클래스를 생성하는 것입니다. `pyside6-uic` 도구가 있기 때문에 가능합니다. 이 도구를 사용하려면 콘솔에서 다음 커맨드를 실행해야 합니다:

```
pyside6-uic mainwindow.ui -o ui_mainwindow.py
```

커맨드의 모든 출력을 `ui_mainwindow.py` 파일에 리다이렉트하면 직접 import 될 것입니다:

```python
import sys
from PySide6.QtWidgets import QApplication, QMainWindow
from PySide6.QtCore import QFile
from ui_mainwindow import Ui_MainWindow    # UI 파일에서 가져온 위젯 클래스

class MainWindow(QMainWindow):
    def __init__(self):
        super(MainWindow, self).__init__()
        # UI 파일로부터 생성된 Python 클래스를 로딩함
        self.ui = Ui_MainWindow()
        self.ui.setupUi(self)

if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    sys.exit(app.exec())
```

주의: UI 파일이 바뀔 때마다 `pyside6-uic`를 또 실행해야 합니다.

#### 선택 B: 직접 로딩하기

```python
# File: main.py
import sys
from PySide6.QtUiTools import QUiLoader      # UI 파일을 직접 로드하기 위해 필요한 모듈
from PySide6.QtWidgets import QApplication
from PySide6.QtCore import QFile, QIODevice

if __name__ == "__main__":
    app = QApplication(sys.argv)

    # UI 파일 이름 지정
    ui_file_name = "mainwindow.ui"
    ui_file = QFile(ui_file_name)
    if not ui_file.open(QIODevice.ReadOnly):
        print(f"Cannot open {ui_file_name}: {ui_file.errorString()}")
        sys.exit(-1)
    # UI 파일을 동적으로 로드함
    loader = QUiLoader()
    window = loader.load(ui_file)

    ui_file.close()
    if not window:
        print(loader.errorString())
        sys.exit(-1)
    window.show()

    sys.exit(app.exec())
```

커맨드 프롬프트에서 다음과 같이 실행하면 됩니다:

```
python main.py
```

주의: `QUiLoader`는 시그널/슬롯 연결을 위해 문자열 인수 형태로 함수 시그니처를 취하는 `connect()` 호출을 사용합니다. 내부적으로 서로 다른 C++ 타입에 맵핑되기 때문에 Python에서 작성한 커스텀 위젯의 `str` 또는 `list`와 같은 Python 타입은 처리할 수 없습니다.

#### Qt Designer에서의 커스텀 위젯

Qt Designer는 커스텀 위젯을 사용할 수 있습니다. 커스텀 위젯은 위젯 박스 안에 있으며 Qt 위젯처럼 폼에 드래그할 수 있습니다. ([Qt Designer로 커스텀 위젯 사용하기](https://doc.qt.io/qt-6/designer-using-custom-widgets.html)를 보십시오) 일반적으로 [QDesignerCustomWidgetInterface](https://doc.qt.io/qt-6/qdesignercustomwidgetinterface.html)를 구현하는 C++로 작성된 Qt Designer에 플러그인으로 위젯을 구현해야 합니다.

Qt for Python은 `registerCustomWidget()`와 마찬가지로 이를 위한 간단한 인터페이스를 제공합니다.

widgetbinding 예제(`wigglywidget.py`) 또는 taskmenuextension 예제(`tictactoe.py`)에 나온 것처럼 위젯이 Python 모듈로 제공되어야 합니다.

이름이 `register*.py`인 등록 스크립트를 제공하고 path-타입 환경 변수 `PYSIDE_DESIGNER_PLUGINS`가 해당 디렉토리를 가리키게 해서 이 위젯을 Qt Designer에 등록할 수 있습니다.

등록 스크립트의 코드는 다음과 같습니다:

```python
# File: registerwigglywidget.py
from wigglywidget import WigglyWidget

import QtDesigner


TOOLTIP = "A cool wiggly widget (Python)"
DOM_XML = """
<ui language='c++'>
    <widget class='WigglyWidget' name='wigglyWidget'>
        <property name='geometry'>
            <rect>
                <x>0</x>
                <y>0</y>
                <width>400</width>
                <height>200</height>
            </rect>
        </property>
        <property name='text'>
            <string>Hello, world</string>
        </property>
    </widget>
</ui>
"""

QPyDesignerCustomWidgetCollection.registerCustomWidget(WigglyWidget, module="wigglywidget", tool_tip=TOOLTIP, xml=DOM_XML)
```

QPyDesignerCustomWidgetCollection은 [QDesignerCustomWidgetCollectionInterface](https://doc.qt.io/qt-6/qdesignercustomwidgetcollectioninterface.html) 구현을 제공합니다. 이것은 정적 편의 함수로 타입 등록을 위해 Qt Designer에 커스텀 위젯을 노출시키거나, [QDesignerCustomWidgetInterface](https://doc.qt.io/qt-6/qdesignercustomwidgetinterface.html) 인스턴스를 추가할 수 있게 해줍니다.

함수 [`registerCustomWidget()`](https://doc.qt.io/qtforpython-6/PySide6/QtDesigner/QPyDesignerCustomWidgetCollection.html#PySide6.QtDesigner.QPyDesignerCustomWidgetCollection.registerCustomWidget)은 Qt Designer에 위젯 타입을 등록하는 데 사용합니다. 단순한 경우에는 `QUiLoader.registerCustomWidget()`처럼 사용될 수 있습니다. 이 함수는 인수로 커스텀 위젯 타입, 그리고 [QDesignerCustomWidgetInterface](https://doc.qt.io/qt-6/qdesignercustomwidgetinterface.html)의 getter에 해당하는 값들을 전달하는 선택적인 키워드 인수 몇 가지를 필요로 합니다:

`pyside6-designer`를 통해 Qt Designer를 실행할 때, 커스텀 위젯은 위젯 박스 안에 보여야 합니다.

고급 사용법의 경우, [`addCustomWidget()`](https://doc.qt.io/qtforpython-6/PySide6/QtDesigner/QPyDesignerCustomWidgetCollection.html#PySide6.QtDesigner.QPyDesignerCustomWidgetCollection.addCustomWidget)에게 타입 대신 클래스 QDesignerCustomWidgetInterface 구현 함수를 전달할 수도 있습니다. 이것은 커스텀 위젯에 대해 커스텀 컨텐스트 메뉴가 등록된 taskmenuextension 예제에서 볼 수 있습니다. 예제는 C++ [Task Menu Extension 예제](https://doc.qt.io/qt-6/qtdesigner-taskmenuextension-example.html)의 포팅 버전입니다.

#### Qt Designer 플러그인 문제해결하기

`pyside6-designer`를 반드시 사용해야 합니다. 독립형 Qt Designer는 플러그인을 로드하지 않습니다.

메뉴 항목 Help/About Plugin은 발견한 플러그인과 잠재적인 로드 오류 메시지를 보여주는 다이얼로그를 표시합니다.

더 많은 오류 메시지는 콘솔 또는 Windows Debug 뷰를 확인하십시오.

Python에 의한 출력 버퍼링 때문에 Qt Designer가 종류된 후에 오류 메시지가 나타날 수도 있습니다.

Qt for Python을 필드할 때, 플러그인을 제대로 설치하기 위해 `--standalone` 옵션을 설정하시기 바랍니다.

### `.qrc` 파일 사용하기 (`pyside6-rcc`)

[Qt 리소스 시스템](https://doc.qt.io/qt-5/resources.html)은 애플리케이션에 바이너리 파일을 저장하기 위한 메커니즘입니다.

애플리케이션에 내장되는 파일은 `QFile` 클래스로 접근할 수 있으며 `QIcon`과 `QPixmap` 클래스의 생성자는 `:/`로 시작하는 특수한 파일 이름을 사용하여 파일 이름을 취할 수 있습니다.

가장 일반적인 용도는 커스텀 이미지, 아이콘, 글꼴 등을 가져오는 것입니다.

이 튜토리얼에서는 커스텀 이미지를 버튼 아이콘으로 사용하는 방법을 배울 것입니다.

영감을 주기 위해 Qt의 멀티미디어 플레이어 예제를 각색해 보겠습니다.

다음 이미지에서 볼 수 있듯이, 미디어 액션(재생, 일시정지, 정지 등)을 위해 `QPushButton`을 사용합니다. 그리고 현재 기본 아이콘을 사용하고 있습니다.

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/e3ff4478-0fe2-48a9-a167-3a7c15c5bd62)

아이콘을 직접 설계해서 애플리케이션을 더 매력적으로 만들 수 있겠지만, 원치 않는다면 다운로드한 예제를 사용하셔도 됩니다.

[아이콘 다운로드하기](https://doc.qt.io/qtforpython-6/_downloads/567affe64a389e6429072ff0b635887a/icons.zip)

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/be5ce1e8-195c-4d2e-8cc7-a434ae95755b)

[Qt 리소스 시스템](https://doc.qt.io/qt-5/resources.html) 사이트에서 `rcc` 커맨드, `.qrc` 파일 포맷, 리소스 시스템에 대한 전반적인 내용을 찾을 수 있습니다.

#### `.qrc` 파일

커맨드 실행에 앞서, `.qrc` 파일에 리소스에 대한 정보를 추가하십시오. 다음 예제에서 `icons.qrc` 안에 리소스가 어떻게 나열되어 있는지 보십시오.

```xml
</ui>
<!DOCTYPE RCC><RCC version="1.0">
<qresource>
    <file>icons/play.png</file>
    <file>icons/pause.png</file>
    <file>icons/stop.png</file>
    <file>icons/previous.png</file>
    <file>icons/forward.png</file>
</qresource>
</RCC>
```

#### Python 파일 생성하기

이제 `icons.qrc` 파일이 준비 되었습니다. 리소스에 대한 바이너리 정보를 포함하는 Python 클래스를 생성하기 위해 `pyside6-rcc` 도구를 사용하십시오.

그러기 위해서는 다음을 실행해야 합니다:

```
pyside6-rcc icons.qrc -o rc_icons.py
```

`-o` 옵션은 출력 파일 이름을 지정할 수 있습니다. 이 경우 출력 파일의 이름은 `rc_icons.py`입니다.

생성된 파일을 사용하려면, 당신의 메인 Python 파일 최상단에 다음 import 문을 추가하십시오:

```python
import rc_icons
```

#### 코드 바꾸기

기존 예제를 수정하려면, 다음 라인을 변경해야 합니다:

```python
from PySide6.QtGui import QIcon, QKeySequence
playIcon = self.style().standardIcon(QStyle.SP_MediaPlay)
previousIcon = self.style().standardIcon(QStyle.SP_MediaSkipBackward)
pauseIcon = self.style().standardIcon(QStyle.SP_MediaPause)
nextIcon = self.style().standardIcon(QStyle.SP_MediaSkipForward)
stopIcon = self.style().standardIcon(QStyle.SP_MediaStop)
```

위의 내용을 다음과 같이 바꾸십시오:

```python
from PySide6.QtGui import QIcon, QKeySequence, QPixmap
playIcon = QIcon(QPixmap(":/icons/play.png"))
previousIcon = QIcon(QPixmap(":/icons/previous.png"))
pauseIcon = QIcon(QPixmap(":/icons/pause.png"))
nextIcon = QIcon(QPixmap(":/icons/forward.png"))
stopIcon = QIcon(QPixmap(":/icons/stop.png"))
```

이렇게 하면 애플리케이션 테마가 제공하는 기본 아이콘 대신 새로운 아이콘을 사용하게 됩니다.

다른 import 구문 밑에 다음을 추가하십시오.

```python
import rc_icons
```

이제 클래스의 생성자는 다음과 같습니다:

```python
def __init__(self):
    super(MainWindow, self).__init__()

    self.playlist = QMediaPlaylist()
    self.player = QMediaPlayer()

    toolBar = QToolBar()
    self.addToolBar(toolBar)

    fileMenu = self.menuBar().addMenu("&File")
    openAction = QAction(QIcon.fromTheme("document-open"),
                         "&Open...", self, shortcut=QKeySequence.Open,
                         triggered=self.open)
    fileMenu.addAction(openAction)
    exitAction = QAction(QIcon.fromTheme("application-exit"), "E&xit",
                         self, shortcut="Ctrl+Q", triggered=self.close)
    fileMenu.addAction(exitAction)

    playMenu = self.menuBar().addMenu("&Play")
    playIcon = QIcon(QPixmap(":/icons/play.png"))
    self.playAction = toolBar.addAction(playIcon, "Play")
    self.playAction.triggered.connect(self.player.play)
    playMenu.addAction(self.playAction)

    previousIcon = QIcon(QPixmap(":/icons/previous.png"))
    self.previousAction = toolBar.addAction(previousIcon, "Previous")
    self.previousAction.triggered.connect(self.previousClicked)
    playMenu.addAction(self.previousAction)

    pauseIcon = QIcon(QPixmap(":/icons/pause.png"))
    self.pauseAction = toolBar.addAction(pauseIcon, "Pause")
    self.pauseAction.triggered.connect(self.player.pause)
    playMenu.addAction(self.pauseAction)

    nextIcon = QIcon(QPixmap(":/icons/forward.png"))
    self.nextAction = toolBar.addAction(nextIcon, "Next")
    self.nextAction.triggered.connect(self.playlist.next)
    playMenu.addAction(self.nextAction)

    stopIcon = QIcon(QPixmap(":/icons/stop.png"))
    self.stopAction = toolBar.addAction(stopIcon, "Stop")
    self.stopAction.triggered.connect(self.player.stop)
    playMenu.addAction(self.stopAction)

    # 여러 라인들이 생략됨
```

#### 예제 실행하기

새로운 아이콘 집합을 확인하려면 `python main.py`를 호출하여 애플리케이션을 실행해 보십시오:

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/82479472-8349-4245-9ab3-cd199e3b4f48)

### 애플리케이션 번역하기

#### Qt Linguist

Qt Linguist 및 관련 도구는 애플리케이션을 위한 번역을 제공하는 데 사용할 수 있습니다.

`examples/widgets/linguist` 예제가 이것을 설명하고 있습니다. 예제는 매우 간단한데 메뉴가 하나 있고 다중 선택이 가능한 프로그래밍 언어 리스트를 보여줍니다.

번역을 찾아보는 함수 호출을 통해 메시지 문자열을 전달하여 번역이 이루어집니다. 각 `QObject` 인스턴스는 번역을 위한 `tr()` 함수를 제공합니다. 또, 비-QObject 클래스에 번역된 텍스트를 추가하는 `QCoreApplication.translate()` 함수도 있습니다.

Qt는 오류 메시지 및 표준 다이얼로그 캡션을 포함하여 자체 번역을 탑재하고 있습니다.

linguist 예제에는 `self.tr()`에 들어 있는 여러 메시지들이 나옵니다. 선택 변경에 따라 나오는 상태 바 메시지는 개수에 따라 복수형을 사용합니다:

```python
count = len(self._list_widget.selectionModel().selectedRows())
message = self.tr("%n language(s) selected", "", count)
```

예제에 대한 번역 워크플로우는 다음과 같습니다: 번역된 메시지는 `lupdate` 도구를 사용하여 추출할 수 있고 XML-기반 `.ts` 파일이 생성됩니다:

```python
pyside6-lupdate main.py -ts example_de.ts
```

만약 `example_de.ts`가 이미 존재한다면, 코드에 새로운 메시지가 추가되어 업데이트됩니다.

만약 프로젝트에 폼 파일(`.ui`)과 QML 파일(`.qml`)이 있다면, 마찬가지로 `pyside6-lupdate` 도구에 전달해야 합니다:

```python
pyside6-lupdate main.py main.qml form.ui -ts example_de.ts
```

폼 파일로부터 `pyside6-uic`에 의해 생성된 소스 파일은 전달하지 말아야 합니다.

`.ts` 파일은 Qt Linguist를 사용하여 번역합니다. 일단 완료되면, 파일은 바이너리 형태(`.qm` 파일)로 변환됩니다:

```
mkdir translations
pyside6-lrelease example_de.ts -qm translations/example_de.qm
```

`.qm` 파일을 탑재하고 싶지 않다면, 아이콘이나 다른 애플리케이션 리소스처럼 Qt 리소스 파일로 두는 것을 권장합니다. ([.qrc 파일 사용하기 (pyside6-rcc)](https://doc.qt.io/qtforpython-6/tutorials/basictutorial/qrcfiles.html#using-qrc-files)를 보십시오) 리소스 파일 `linguist.qrc`는 `:/translations` 아래의 `example_de.qm`을 제공합니다:

```xml
<!DOCTYPE RCC><RCC version="1.0">
<qresource>
    <file>translations/example_de.qm</file>
</qresource>
</RCC>
```

런타임 시에, `QTranslator` 클래스를 사용하여 번역 내용을 로드해야 합니다:

```python
path = QLibraryInfo.location(QLibraryInfo.TranslationsPath)
translator = QTranslator(app)
if translator.load(QLocale.system(), 'qtbase', '_', path):
    app.installTranslator(translator)
translator = QTranslator(app)
path = ':/translations'
if translator.load(QLocale.system(), 'example', '_', path):
    app.installTranslator(translator)
```

이 코드는 먼저 Qt에 내장된 번역을 로드하고, 그 다음에 리소스로부터 로드된 애플리케이션의 번역을 로드합니다.

그 다음에는 예제는 독일어로 실행될 수 있습니다:

```
LANG=de python main.py
```

#### GNU gettext

[GNU gettext](https://docs.python.org/3/library/gettext.html) 모듈은 애플리케이션 번역을 제공하는 데 사용할 수 있습니다.

`examples/widgets/gettext` 예제에서 이것을 설명합니다. 예제는 매우 간단합니다. 메뉴가 하나 있고 다중 선택이 가능한 프로그래밍 언어 리스트를 보여줍니다.

번역을 찾아보는 함수 호출을 통해 메시지 문자열을 전달하여 번역이 이루어집니다. 일반적으로 메인 번역 함수에게 `_`라는 별칭을 붙여줍니다. 개수에 따라 복수형을 포함하는 문장에 대하여 특수 번역 함수가 있습니다. ("{0} items(s) selected") 보통 `ngettext`라는 별칭을 붙여줍니다.

위의 함수들은 최상단에서 정의합니다:

```python
import gettext
...
_ = None
ngettext = None
```

그리고 다음과 같이 나중에 할당됩니다:

```python
src_dir = Path(__file__).resolve().parent
try:
    translation = gettext.translation('example', localedir=src_dir / 'locales')
    if translation:
        translation.install()
        _ = translation.gettext
        ngettext = translation.ngettext
except FileNotFoundError:
    pass
if not _:
    _ = gettext.gettext
    ngettext = gettext.ngettext
```

위의 예제는 번역 파일의 기본 이름이 `example`이며 `locales` 아래의 소스 트리에서 찾을 수 있다고 지정합니다. 이 코드는 현재 언어와 일치하는 번역을 로드하려고 할 것입니다.

번역된 메시지는 다음과 같습니다:

```python
file_menu = self.menuBar().addMenu(_("&File"))
```

선택 변경에 따라 나오는 상태 바 메시지는 개수에 따라 복수형을 사용합니다:

```python
count = len(self._list_widget.selectionModel().selectedRows())
message = ngettext("{0} language selected",
                   "{0} languages selected", count).format(count)
```

`ngettext()` 함수는 단수형, 복수형, 개수를 인수로 취합니다. 리턴된 문자열은 여전히 포맷팅 placeholder를 포함하고 있으므로 `format()`을 통해 전달되어야 합니다.

독일어를 말하도록 메시지를 번역하려면, 템플릿 파일(`.pot`)을 먼저 생성해야 합니다:

```
mkdir -p locales/de_DE/LC_MESSAGES
xgettext -L Python -o locales/example.pot main.py
```

이 파일은 적절한 값에 의해 치환될 수 있는 여러 가지 제네릭 placeholder를 가지고 있습니다. 그리고 나서 `de_DE/LC_MESSAGES` 디렉토리에 복사됩니다.

```
cd locales/de_DE/LC_MESSAGES/
cp ../../example.pot .
```

독일어 복수형과 부호화를 설명하기 위해 더 많은 각색이 필요합니다:

```
"Project-Id-Version: PySide6 gettext example\n"
"POT-Creation-Date: 2021-07-05 14:16+0200\n"
"Language: de_DE\n"
"MIME-Version: 1.0\n"
"Content-Type: text/plain; charset=UTF-8\n"
"Content-Transfer-Encoding: 8bit\n"
"Plural-Forms: nplurals=2; plural=n != 1;\n"
```

다음에는 번역된 메시지가 주어질 수 있습니다:

```
#: main.py:57
msgid "&File"
msgstr "&Datei"
```

마지막으로 `.pot`은 배치되어야 하는 바이너리 형태(machine object 파일, `.mo`)로 변환됩니다.

```
msgfmt -o example.mo example.pot
```

그리고 나서 예제를 독일어로 실행할 수 있습니다::

```
LANG=de python main.py
```

### 위젯 애플리케이션 스타일 꾸미기

Qt Widgets 애플리케이션은 플랫폼에 종속적인 기본 테마를 사용합니다. 일부의 경우, Qt 테마를 변경하는 시스템 전체 구성이 있을 수 있으며 애플리케이션이 다르게 표시됩니다.

그러나 커스텀 위젯을 관리하고 각 구성요소에 커스텀 스타일을 제공할 수 있습니다. 예를 들어, 다음과 같은 간단한 코드를 봅시다:

```python
import sys
from PySide6.QtCore import Qt
from PySide6.QtWidgets import QApplication, QLabel

if __name__ == "__main__":
    app = QApplication()
    w = QLabel("This is a placeholder text")
    w.setAlignment(Qt.AlignCenter)
    w.show()
    sys.exit(app.exec())
```

이 코드를 실행하면, 당신은 placeholder text가 붙어 있는 중앙에 정렬된 간단한 `QLabel`을 보게 될 것입니다.

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/029f40e3-7c78-41e8-a824-3aa2ef09931a)

CSS-유사 문법을 사용하여 애플리케이션의 스타일을 꾸밀 수 있습니다. 더 많은 정보는 [Qt 스타일 시트 레퍼런스](https://doc.qt.io/qt-5/stylesheet-reference.html)를 보십시오.

`background-color`와 `font-family` 같은 CSS 속성을 설정하여 `QLabel`의 스타일을 꾸밀 수 있습니다. 이제 이 변경사항에 따라 코드가 어떻게 표시되는지 봅시다:

```python
import sys
from PySide6.QtCore import Qt
from PySide6.QtWidgets import QApplication, QLabel

if __name__ == "__main__":
    app = QApplication()
    w = QLabel("This is a placeholder text")
    w.setAlignment(Qt.AlignCenter)
    # 추가된 부분
    w.setStyleSheet("""
        background-color: #262626;
        color: #FFFFFF;
        font-family: Titillium;
        font-size: 18px;
        """)
    w.show()
    sys.exit(app.exec())
```

이제 코드를 실행하면 커스텀 스타일에 따라 QLabel이 다르게 보이는 것을 확인하실 수 있습니다:

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/08c6a61e-fff2-4587-b9f8-2de965f2949c)

주의: 만약 글꼴 `Titillium`이 설치되어 있지 않다면, 다른 방법을 사용해 보실 수 있습니다. `QFontDatabase`, 특히 `families()` 메서드를 사용하여 설치된 글꼴의 목록을 보실 수 있습니다.

각 UI 요소의 스타일을 일일이 꾸미는 것은 많은 작업을 필요로 합니다. 그 대안으로 Qt 스타일 시트를 사용하면 됩니다. Qt 스타일 시트란 애플리케이션의 UI 요소에 대한 스타일을 정의하는 1개 이상의 `.qss` 파일을 의미합니다.

[Qt 스타일 시트 예제](https://doc.qt.io/qt-5/stylesheet-examples.html) 문서 페이지에서 더 많은 예제를 보실 수 있습니다.

#### Qt 스타일 시트

경고: 애플리케이션을 수정하기 전에 애플리케이션의 모든 그래픽 세부사항에 대해 사용자가 책임져야 한다는 것을 명심하십시오. 여백과 크기를 변경하면 이상하게, 혹은 비뚤어져 보일 수 있습니다. 그래서 스타일을 변경하는 것은 신중해야 합니다. 가능한 모든 경우를 대비하기 위해 완전히 새로운 Qt 스타일을 만드는 것을 권장합니다.

`qss` 파일은 CSS 파일과 매우 비슷하지만, Widget 컴포넌트를 지정해야 하고 경우에 따라서는 객체의 이름을 지정해야 합니다:

```css
QLabel {
    background-color: red;
}

QLabel#title {
    font-size: 20px;
}
```

1번째 스타일은 애플리케이션의 모든 `QLabel` 객체에 대한 `background-color`를 정의합니다. 반면, 2번째 스타일은 `title` 객체의 스타일만 정의합니다.

주의: 아무 객체나 `setObjectName(str)` 함수를 이용하여 객체 이름을 설정할 수 있습니다. 예를 들면: `label = QLabel("Test")`의 경우, `label.setObjectName("title")`라고 작성할 수 있습니다.

애플리케이션에 `qss` 파일이 있다면, 해당 파일을 읽고 `QApplication.setStyleSheet(str)` 함수를 사용하여 적용할 수 있습니다:

```python
if __name__ == "__main__":
    app = QApplication()

    w = Widget()
    w.show()

    with open("style.qss", "r") as f:
        _style = f.read()
        app.setStyleSheet(_style)

    sys.exit(app.exec())
```

`qss` 파일을 사용하면 코드에서 스타일 관련 내용을 분리할 수 있습니다. 그리고 스타일을 쉽게 켜고 끌 수 있습니다.

더 많은 위젯 구성요소를 가진 새로운 예제를 봅시다:

```python
class Widget(QWidget):
    def __init__(self, parent=None):
        super(Widget, self).__init__(parent)

        menu_widget = QListWidget()
        for i in range(10):
            item = QListWidgetItem(f"Item {i}")
            item.setTextAlignment(Qt.AlignCenter)
            menu_widget.addItem(item)

        text_widget = QLabel(_placeholder)
        button = QPushButton("Something")

        content_layout = QVBoxLayout()
        content_layout.addWidget(text_widget)
        content_layout.addWidget(button)
        main_widget = QWidget()
        main_widget.setLayout(content_layout)

        layout = QHBoxLayout()
        layout.addWidget(menu_widget, 1)
        layout.addWidget(main_widget, 4)
        self.setLayout(layout)
```

이것은 2개의 컬럼 위젯을 표시하는데, 왼쪽에는 `QListWidget`, 오른쪽에는 `QLabel`과 `QPushButton`가 나옵니다. 코드를 실행하면 다음과 같습니다:

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/206e419e-1192-408e-bd16-3a2369bb8bb7)

앞에서 설명한 `style.qss` 파일에 내용을 추가하면, 예전 예제의 look-n-feel을 변경할 수 있습니다:

```css
QListWidget {
    color: #FFFFFF;
    background-color: #33373B;
}

QListWidget::item {
    height: 50px;
}

QListWidget::item:selected {
    background-color: #2ABf9E;
}

QLabel {
    background-color: #FFFFFF;
    qproperty-alignment: AlignCenter;
}

QPushButton {
    background-color: #2ABf9E;
    padding: 20px;
    font-size: 18px;
}
```

스타일은 주로 여러 위젯의 컬러를 변경하고, 간격을 포함하여 정렬을 변경합니다. 또한 QListWidget 항목에 대해 상태 기반 스타일을 사용할 수도 있습니다. 예를 들면, 선택 여부에 따라 스타일을 다르게 하는 것입니다.

이 주제에 대해 탐색한 스타일을 모두 적용한 후에 `QLabel` 예제가 많이 달라 보일 것입니다. 코드를 실행하여 새로운 모습을 확인해 보십시오:

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/e83301d3-7f5a-4692-b935-1da9c5584f15)

스타일 시트를 자유롭게 조정할 수 있으며 애플리케이션에 멋진 느낌을 줄 수 있습니다.

### 처음 QtQuick/QML 애플리케이션

[QML](https://doc.qt.io/qt-6/qmlapplications.html)은 선언적 언어로서 기존 언어보다 빠르게 애플리케이션을 개발할 수 있게 해줍니다. 선언적 특성 때문에 애플리케이션의 UI를 설계할 때 이상적입니다. QML에서는 프로퍼티를 가진 객체 트리로 사용자 인터페이스를 지정합니다. 이 튜토리얼에서는 PySide6와 QML로 간단한 "Hello World" 애플리케이션을 만드는 방법을 보여드리겠습니다.

PySide6/QML 애플리케이션은 적어도 2개의 파일로 구성되어 있습니다. - 사용자 인터페이스의 QML 설명이 포함된 파일, QML 파일을 로드하는 Python 파일. 쉽게 만들기 위해 2개의 파일을 동일한 디렉토리 안에 저장해 둡시다.

다음은 `view.qml`이라는 간단한 QML 파일입니다:

```qml
import QtQuick

Rectangle {
    id: main
    width: 200
    height: 200
    color: "green"

    Text {
        text: "Hello World"
        anchors.centerIn: main
    }
}
```

QML 모듈인 `QtQuick`을 가져오는 것부터 시작합니다.

QML 코드의 나머지는 이전에 HTML이나 XML 파일을 사용한 사람들에게는 매우 간단할 것입니다. 기본적으로 크기가 200*200인 녹색 직사각형을 만들고 "Hello World"라고 읽는 Text 요소를 추가할 것입니다. 코드 `anchors.centerIn: main`은 `id: main`인 객체 내부의 중심에 텍스트가 나오게 합니다. 여기서 `id: main`는 Rectangle입니다.

이제 PySide6에서 코드가 어떻게 나오는지 봅시다. `main.py`를 호출해 봅시다:

```python
import sys
from PySide6.QtWidgets import QApplication
from PySide6.QtQuick import QQuickView

if __name__ == "__main__":
    app = QApplication()
    view = QQuickView()

    view.setSource("view.qml")
    view.show()
    sys.exit(app.exec())
```

만약 PySide6가 익숙하고 튜토리얼을 잘 따라왔다면, 이미 이 코드를 여러 번 보았을 것입니다. 다른 점이라면 `import QtQuick`을 반드시 넣어야 하고 `QQuickView` 객체의 소스를 QML 파일의 URL로 설정해야 한다는 것입니다. `QQuickView.show()`를 호출하면 Qt 위젯과 비슷한 것을 보실 것입니다.

주의: 만약 데스크톱용 프로그램을 만들고 있다면, view를 보여주기 전에 `view.setResizeMode(QQuickView.SizeRootObjectToView)`를 추가하는 것을 고려해야 합니다.

`main.py` 스크립트를 실행하면, 다음 애플리케이션을 보게 될 것입니다:

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/b0759d76-4e22-4baf-9968-0a7e97edc971)

### Python-QML 통합

This tutorial provides a quick walk-through of a python application that loads, and interacts with a QML file. QML is a declarative language that lets you design UIs faster than a traditional language, such as C++. The QtQml and QtQuick modules provides the necessary infrastructure for QML-based UIs.

In this tutorial, you will learn how to integrate Python with a QML application. This mechanism will help us to understand how to use Python as a backend for certain signals from the UI elements in the QML interface. Additionally, you will learn how to provide a modern look to your QML application using one of the features from Qt Quick Controls 2.

The tutorial is based on an application that allow you to set many text properties, like increasing the font size, changing the color, changing the style, and so on. Before you begin, install the PySide6 Python packages.

The following step-by-step process will guide you through the key elements of the QML based application and PySide6 integration:

1. First, let’s start with the following QML-based UI:

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/896fb08e-8904-47c9-89eb-8189f4819d23)

The design is based on a GridLayout, containing two ColumnLayout. Inside the UI you will find many RadioButton, Button, and a Slider.

2. With the QML file in place, you can load it from Python:

```python

if __name__ == '__main__':
    app = QGuiApplication(sys.argv)
    QQuickStyle.setStyle("Material")
    engine = QQmlApplicationEngine()

    # Get the path of the current directory, and then add the name
    # of the QML file, to load it.
    qml_file = Path(__file__).parent / 'view.qml'
    engine.load(qml_file)

    if not engine.rootObjects():
        sys.exit(-1)
```

Notice that we only need a `QQmlApplicationEngine` to `load` the QML file.

3. Define the `Bridge` class, containing all the logic for the element that will be register in QML:

```python
# To be used on the @QmlElement decorator
# (QML_IMPORT_MINOR_VERSION is optional)
QML_IMPORT_NAME = "io.qt.textproperties"
QML_IMPORT_MAJOR_VERSION = 1


@QmlElement
class Bridge(QObject):

    @Slot(str, result=str)
    def getColor(self, s):
        if s.lower() == "red":
            return "#ef9a9a"
        elif s.lower() == "green":
            return "#a5d6a7"
        elif s.lower() == "blue":
            return "#90caf9"
        else:
            return "white"

    @Slot(float, result=int)
    def getSize(self, s):
        size = int(s * 34)
        if size <= 0:
            return 1
        else:
            return size

    @Slot(str, result=bool)
    def getItalic(self, s):
        if s.lower() == "italic":
            return True
        else:
            return False

    @Slot(str, result=bool)
    def getBold(self, s):
        if s.lower() == "bold":
            return True
        else:
            return False
```

Notice that the registration happens thanks to the `QmlElement` decorator, that underneath uses the reference to the `Bridge` class and the variables `QML_IMPORT_NAME` and `QML_IMPORT_MAJOR_VERSION`.

4. Now, go back to the QML file and connect the signals to the slots defined in the `Bridge` class:

```qml
Bridge {
   id: bridge
}
```

Inside the `ApplicationWindow` we declare a component with the same name as the Python class, and provide an `id:`. This `id` will help you to get a reference to the element that was registered from Python.

```qml
            RadioButton {
                id: italic
                Layout.alignment: Qt.AlignLeft
                text: "Italic"
                onToggled: {
                    leftlabel.font.italic = bridge.getItalic(italic.text)
                    leftlabel.font.bold = bridge.getBold(italic.text)
                    leftlabel.font.underline = bridge.getUnderline(italic.text)

                }
            }
```

The properties Italic, Bold, and Underline are mutually exclusive, this means only one can be active at any time. To achieve this each time we select one of these options, we check the three properties via the QML element property as you can see in the above snippet. Only one of the three will return True, while the other two will return False, that is how we make sure only one is being applied to the text.

5. Each slot verifies if the selected option contains the text associated to the property:

```python
    @Slot(str, result=bool)
    def getItalic(self, s):
        if s.lower() == "italic":
            return True
        else:
            return False
```

Returning True or False allows you to activate and deactivate the properties of the QML UI elements.

It is also possible to return other values that are not Boolean, like the slot in charge of returning the font size:

```python
    @Slot(float, result=int)
    def getSize(self, s):
        size = int(s * 34)
        if size <= 0:
            return 1
        else:
```

6. Now, for changing the look of our application, you have two options:

1) Use the command line: execute the python file adding the option, `--style`:

```
python main.py --style material
```

2) Use a `qtquickcontrols2.conf` file:

```
[Controls]
Style=Material

[Universal]
Theme=System
Accent=Red

[Material]
Theme=Dark
Accent=Red
```

Then add it to your `.qrc` file:

```xml
<!DOCTYPE RCC><RCC version="1.0">
<qresource prefix="/">
    <file>qtquickcontrols2.conf</file>
</qresource>
</RCC>
```

Generate the rc file running, `pyside6-rcc style.qrc -o style_rc.py` And finally import it from your `main.py` script.

```python
import sys
from pathlib import Path

from PySide6.QtCore import QObject, Slot
from PySide6.QtGui import QGuiApplication
from PySide6.QtQml import QQmlApplicationEngine, QmlElement
from PySide6.QtQuickControls2 import QQuickStyle

import style_rc
```

You can read more about this configuration file [here](https://doc.qt.io/qt-5/qtquickcontrols2-configuration.html).

The final look of your application will be:

![image](https://github.com/Soonbum/Qt_for_Python/assets/16474083/74253d81-9f2a-4321-b171-35ad61192cad)

다운로드 링크: [view.qml](https://doc.qt.io/qtforpython-6/_downloads/e1a28f9b527916d7a11c2cc59003a570/view.qml)

다운로드 링크: [main.py](https://doc.qt.io/qtforpython-6/_downloads/63044c6135ff9f549e8ff800a8b2a856/main.py)

... https://doc.qt.io/qtforpython-6/tutorials/qmlintegration/qmlintegration.html

### QML 애플리케이션 튜토리얼

... https://doc.qt.io/qtforpython-6/tutorials/qmlapp/qmlapplication.html

### QML, SQL 및 PySide 통합 튜토리얼

... https://doc.qt.io/qtforpython-6/tutorials/qmlsqlintegration/qmlsqlintegration.html

### 파일 시스템 탐색기 확장하기 예제

... https://doc.qt.io/qtforpython-6/tutorials/extendedexplorer/extendedexplorer.html

### 데이터 시각화 도구 튜토리얼

... https://doc.qt.io/qtforpython-6/tutorials/datavisualize/index.html

### 경비 계산 도구 튜토리얼

... https://doc.qt.io/qtforpython-6/tutorials/expenses/expenses.html

### Qt 개요

... https://doc.qt.io/qtforpython-6/overviews/overviews-main.html

### C++ 애플리케이션을 Python으로 포팅하기

... https://doc.qt.io/qtforpython-6/tutorials/portingguide/index.html

### PySide6 애플리케이션의 C++ 확장 디버그하는 방법

... https://doc.qt.io/qtforpython-6/tutorials/debugging/mixed_debugging.html

## 예제

## 비디오

## 배포

## 고려사항
