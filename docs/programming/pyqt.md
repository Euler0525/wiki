# PyQt Examples

## Signal And Slot

```python
import sys

from random import choice
from PyQt6.QtCore import QSize
from PyQt6.QtWidgets import QApplication, QMainWindow, QPushButton

window_titles = [
    "App1",
    "App2",
    "App3",
    "App4",
    "App5",
    "App6",
    "Error"
]


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.window_title = "My App"
        self.setWindowTitle(self.window_title)

        # self.setFixedSize(QSize(400, 300))
        self.setMinimumSize(QSize(400, 300))
        self.setMaximumSize(QSize(800, 600))

        self.button = QPushButton("Press Me!")
        self.button.setText("Never clicked!")

        # Initial value
        self.button.setCheckable(True)
        self.button.setChecked(False)

        self.button_is_checked = False

        # connect: signal -> slot
        self.windowTitleChanged.connect(self.the_window_title_changed)
        self.button.clicked.connect(self.the_button_was_clicked)
        self.button.clicked.connect(self.the_button_was_toggled)
        self.button.released.connect(self.the_button_was_released)

        self.setCentralWidget(self.button)

    def the_button_was_clicked(self):
        print("Clicked!")
        self.button.setText("Already clicked!")
        # self.button.setEnabled(False)
        self.window_title = choice(window_titles)
        self.setWindowTitle(self.window_title)

    def the_button_was_toggled(self, is_checked):
        print("Checked?", is_checked)

        self.button_is_checked = is_checked
        print(self.button_is_checked)

    def the_button_was_released(self):
        self.button_is_checked = self.button.isChecked()
        print(self.button_is_checked)

    def the_window_title_changed(self):
        if (self.window_title == "Error"):
            self.button.setDisabled(True)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

```python
import sys

from PyQt6.QtWidgets import (
    QApplication,
    QWidget,
    QMainWindow,
    QLabel,
    QLineEdit,
    QVBoxLayout,
)


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        self.label = QLabel()

        self.input = QLineEdit()
        self.input.textChanged.connect(self.label.setText)  # <1>

        layout = QVBoxLayout()  # <2>
        layout.addWidget(self.input)
        layout.addWidget(self.label)

        container = QWidget()
        container.setLayout(layout)

        # Set the central widget of the Window.
        self.setCentralWidget(container)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

## Widgets

```python
import os
import sys

from PyQt6.QtCore import Qt
from PyQt6.QtGui import QPixmap
from PyQt6.QtWidgets import QApplication, QLabel, QMainWindow

curr_dir = os.path.dirname(__file__)
print("Current folder: ", os.getcwd())
print("Relative to: ", curr_dir)


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        widget = QLabel("Hello World!")
        font = widget.font()
        font.setPointSize(50)
        widget.setFont(font)
        widget.setAlignment(
            Qt.AlignmentFlag.AlignHCenter | Qt.AlignmentFlag.AlignVCenter
        )

        widget.setPixmap(QPixmap(os.path.join(curr_dir, "1.png")))
        widget.setScaledContents(True)  # The image can be resized adaptively

        self.setCentralWidget(widget)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

```python
import sys

from PyQt6.QtCore import Qt
from PyQt6.QtWidgets import QApplication, QCheckBox, QMainWindow


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        widget = QCheckBox("This is a checkbox")
        """
        Qt.CheckState.Checked
        Qt.CheckState.Unchecked
        Qt.CheckState.PartiallyChecked
        """
        widget.setCheckState(Qt.CheckState.Unchecked)
        widget.setTristate(True)  # Three-state checkbox

        widget.stateChanged.connect(self.show_state)

        self.setCentralWidget(widget)

    def show_state(self, state):
        print(Qt.CheckState(state) == Qt.CheckState.Checked)
        # Or: state == Qt.CheckState.Checked.value
        print(state)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

```python
import sys

from PyQt6.QtWidgets import QApplication, QComboBox, QMainWindow


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("My App")

        widget = QComboBox()
        widget.addItems(["One", "Two", "Three"])

        widget.currentIndexChanged.connect(self.index_changed)
        widget.currentTextChanged.connect(self.text_changed)

        widget.setEditable(True)
        """
        | 标志	                                      行为
        | :-----------------------------------------: | :-------------: |
        | QComboBox.InsertPolicy.NoInsert             | 不允许插入        |
        | QComboBox.InsertPolicy.InsertAtTop          | 插入为第一个项     |
        | QComboBox.InsertPolicy.InsertAtCurrent      | 替换当前选中的项   |
        | QComboBox.InsertPolicy.InsertAtBottom       | 在最后一项之后插入  |
        | QComboBox.InsertPolicy.InsertAfterCurrent   | 在当前项之后插入   |
        | QComboBox.InsertPolicy.InsertBeforeCurrent  | 在当前项之前插入   |
        | QComboBox.InsertPolicy.InsertAlphabetically | 按字母顺序插入     |
        """
        widget.setInsertPolicy(QComboBox.InsertPolicy.InsertAlphabetically)
        widget.setMaxCount(10)

        self.setCentralWidget(widget)

    def index_changed(self, i):
        print(i)

    def text_changed(self, s):
        print(s)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

``` python
import sys

from PyQt6.QtWidgets import QApplication, QListWidget, QMainWindow


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("My App")

        self.widget = QListWidget()
        self.widget.addItems(["One", "Two", "Three"])

        self.widget.currentItemChanged.connect(self.item_changed)
        self.widget.currentTextChanged.connect(self.text_changed)

        self.setCentralWidget(self.widget)

    def item_changed(self, i):
        print(i.text())

    def text_changed(self, s):
        print(s)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

``` python
import sys

from PyQt6.QtWidgets import QApplication, QListWidget, QMainWindow


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("My App")

        self.lwidget = QListWidget()
        self.lwidget.addItems(["One", "Two", "Three"])

        # Multi select
        self.lwidget.setSelectionMode(QListWidget.SelectionMode.MultiSelection)

        self.lwidget.currentItemChanged.connect(self.item_changed)
        self.lwidget.currentTextChanged.connect(self.text_changed)
        self.lwidget.selectionModel().selectionChanged.connect(self.select_changed)

        self.setCentralWidget(self.lwidget)

    def item_changed(self, i):
        print(i.text())

    def text_changed(self, s):
        print(s)

    def select_changed(self):
        print("Selected items: ", self.lwidget.selectedItems())


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

``` python
import sys

from PyQt6.QtWidgets import QApplication, QLineEdit, QMainWindow


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        self.widget = QLineEdit()
        self.widget.setMaxLength(10)
        self.widget.setPlaceholderText("Enter your text")
        self.widget.setInputMask(" 000.000.000.000;_")

        # widget.setReadOnly(True)  # uncomment to make readonly

        self.widget.textChanged.connect(self.text_changed)
        self.widget.textEdited.connect(self.text_edited)
        self.widget.returnPressed.connect(self.return_pressed)
        self.widget.selectionChanged.connect(self.selection_changed)

        self.setCentralWidget(self.widget)

    def return_pressed(self):
        print("Return pressed!")
        self.centralWidget().setText("BOOM!")

    def selection_changed(self):
        print("Selection changed")
        print(self.centralWidget().selectedText())

    def text_changed(self, s):
        print("Text changed...")
        print(s)

    def text_edited(self, s):
        print("Text edited...")
        print(s)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

``` python
import sys

from PyQt6.QtWidgets import QApplication, QMainWindow, QSpinBox, QDoubleSpinBox


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        widget = QSpinBox()
        # widget = QDoubleSpinBox()

        widget.setMinimum(-3)
        widget.setMaximum(6)
        # Or: widget.setRange(-3,6)

        widget.setPrefix("$")
        widget.setSuffix("c")
        widget.setSingleStep(1)
        # widget.setSingleStep(0.5)

        widget.valueChanged.connect(self.value_changed)
        widget.textChanged.connect(self.value_changed_str)

        self.setCentralWidget(widget)

    def value_changed(self, i):
        print(i)

    def value_changed_str(self, s):
        print(s)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

``` python
import sys

from PyQt6.QtCore import Qt
from PyQt6.QtWidgets import QApplication, QMainWindow, QSlider


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        # self.widget = QSlider(Qt.Orientation.Vertical)
        self.widget = QSlider(Qt.Orientation.Horizontal)

        self.widget.setMinimum(-3)
        self.widget.setMaximum(6)
        # Or: widget.setRange(-3, 6)

        self.widget.setSingleStep(1)

        self.widget.valueChanged.connect(self.value_changed)
        self.widget.sliderMoved.connect(self.slider_position)
        self.widget.sliderPressed.connect(self.slider_pressed)
        self.widget.sliderReleased.connect(self.slider_released)

        self.setCentralWidget(self.widget)

    def value_changed(self, i):
        print(i)

    def slider_position(self, p):
        print("position", p)

    def slider_pressed(self):
        print("Pressed!")

    def slider_released(self):
        print("Released")


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

``` python
import sys

from PyQt6.QtWidgets import QApplication, QDial, QMainWindow


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        widget = QDial()
        widget.setRange(-10, 10)
        widget.setSingleStep(1)

        widget.valueChanged.connect(self.value_changed)
        widget.sliderMoved.connect(self.slider_position)
        widget.sliderPressed.connect(self.slider_pressed)
        widget.sliderReleased.connect(self.slider_released)

        self.setCentralWidget(widget)

    def value_changed(self, i):
        print(i)

    def slider_position(self, p):
        print("position", p)

    def slider_pressed(self):
        print("Pressed!")

    def slider_released(self):
        print("Released")


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

## Layout

``` python
from PyQt6.QtGui import QColor, QPalette
from PyQt6.QtWidgets import QWidget


class Color(QWidget):
    def __init__(self, color):
        super().__init__()
        self.setAutoFillBackground(True)

        palette = self.palette()
        palette.setColor(QPalette.ColorRole.Window, QColor(color))
        self.setPalette(palette)
```

---

``` python
import sys

from layout_colorwidget import Color

from PyQt6.QtWidgets import QApplication, QMainWindow, QWidget, QVBoxLayout, QHBoxLayout


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        # self.layout = QVBoxLayout()
        layout = QHBoxLayout()
        layout1 = QVBoxLayout()

        layout.addWidget(Color("red"))

        layout1.addWidget(Color("yellow"))
        layout1.addWidget(Color("purple"))
        layout1.addWidget(Color("orange"))

        layout.addLayout(layout1)

        layout.addWidget(Color("green"))
        layout.addWidget(Color("blue"))

        layout.setContentsMargins(10, 20, 30, 40)
        layout.setSpacing(5)

        self.widget = QWidget()
        self.widget.setLayout(layout)

        self.setCentralWidget(self.widget)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

``` python
import sys

from layout_colorwidget import Color

from PyQt6.QtWidgets import QApplication, QMainWindow, QWidget, QLabel, QGridLayout


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        layout = QGridLayout()

        layout.addWidget(Color("red"), 0, 0)
        layout.addWidget(Color("yellow"), 2, 2)
        layout.addWidget(Color("purple"), 1, 2)
        layout.addWidget(Color("orange"), 2, 1)
        layout.addWidget(Color("green"), 0, 1)
        layout.addWidget(Color("blue"), 1, 0)

        layout.setContentsMargins(10, 20, 30, 40)
        layout.setSpacing(5)

        self.widget = QWidget()
        self.widget.setLayout(layout)

        self.setCentralWidget(self.widget)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

---

``` python
import sys

from layout_colorwidget import Color

from PyQt6.QtWidgets import QApplication, QMainWindow, QTabWidget, QStackedLayout, QWidget, QVBoxLayout, QHBoxLayout, QPushButton


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        pagelayout = QVBoxLayout()
        button_layout = QHBoxLayout()
        self.stacklayout = QStackedLayout()

        pagelayout.addLayout(button_layout)
        pagelayout.addLayout(self.stacklayout)

        btn = QPushButton("red")
        btn.pressed.connect(self.activate_tab_1)
        button_layout.addWidget(btn)
        self.stacklayout.addWidget(Color("red"))

        btn = QPushButton("green")
        btn.pressed.connect(self.activate_tab_2)
        button_layout.addWidget(btn)
        self.stacklayout.addWidget(Color("green"))

        btn = QPushButton("blue")
        btn.pressed.connect(self.activate_tab_3)
        button_layout.addWidget(btn)
        self.stacklayout.addWidget(Color("blue"))

        widget = QWidget()
        widget.setLayout(pagelayout)
        self.setCentralWidget(widget)

        tabs = QTabWidget()
        tabs.setTabPosition(QTabWidget.TabPosition.North)
        tabs.setMovable(True)

        for color in ["red", "green", "blue", "yellow"]:
            tabs.addTab(Color(color), color)

        self.setCentralWidget(tabs)

    def activate_tab_1(self):
        self.stacklayout.setCurrentIndex(0)

    def activate_tab_2(self):
        self.stacklayout.setCurrentIndex(1)

    def activate_tab_3(self):
        self.stacklayout.setCurrentIndex(2)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

## Toolbars And Menus

> 下载 [bug.png](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/others/bug.png)

``` python
import os
import sys

from PyQt6.QtCore import Qt, QSize
from PyQt6.QtGui import QAction, QIcon, QKeySequence
from PyQt6.QtWidgets import QApplication, QLabel, QMainWindow, QToolBar, QStatusBar, QCheckBox

curr_dir = os.path.dirname(__file__)


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        label = QLabel("Hello!")
        label.setAlignment(Qt.AlignmentFlag.AlignCenter)

        self.setCentralWidget(label)

        toolbar = QToolBar("My main toolbar")
        toolbar.setIconSize(QSize(16, 16))
        # Optional: to prevent this toolbar being removed.
        toolbar.toggleViewAction().setEnabled(False)
        self.addToolBar(toolbar)

        # button_action = QAction("Button", self)
        button_action = QAction(
            QIcon(os.path.join(curr_dir, "bug.png")),
            "Button",
            self
        )
        button_action.setStatusTip("This is your button")
        button_action.triggered.connect(self.onMyToolBarButtonClick)
        button_action.setCheckable(True)
        button_action.setShortcut(QKeySequence("ctrl+q"))
        toolbar.addAction(button_action)

        toolbar.addSeparator()

        button_action1 = QAction(
            QIcon(os.path.join(curr_dir, "bug.png")),
            "Button",
            self
        )
        button_action1.setStatusTip("This is your button1")
        button_action1.triggered.connect(self.onMyToolBarButtonClick)
        button_action1.setCheckable(True)
        toolbar.addAction(button_action1)

        toolbar.addWidget(QLabel("Hello"))
        toolbar.addWidget(QCheckBox())

        self.setStatusBar(QStatusBar(self))

        menu = self.menuBar()

        file_menu = menu.addMenu("&File")
        file_menu.addAction(button_action)
        file_menu.addSeparator()
        file_menu.addAction(button_action1)

        file_submenu = file_menu.addMenu("SubMenu")
        file_submenu.addAction(button_action1)

    def onMyToolBarButtonClick(self, is_checked):
        print("click", is_checked)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

## Dialog

```python
import sys

from PyQt6.QtWidgets import QApplication, QDialog, QLabel, QMainWindow, QPushButton, QDialogButtonBox, QVBoxLayout, QMessageBox


class CusteomDialog(QDialog):
    def __init__(self, parent=None):
        super().__init__(parent)

        self.setWindowTitle("Custom Dialog")

        buttons = (
            QDialogButtonBox.StandardButton.Ok |
            QDialogButtonBox.StandardButton.Cancel
        )
        self.button_box = QDialogButtonBox(buttons)
        self.button_box.accepted.connect(self.accept)
        self.button_box.rejected.connect(self.reject)

        self.layout = QVBoxLayout()
        message = QLabel("Something happened!")
        self.layout.addWidget(message)
        self.layout.addWidget(self.button_box)
        self.setLayout(self.layout)


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        button = QPushButton("Press me for a dialog!")
        # button.clicked.connect(self.button_clicked)
        # button.clicked.connect(self.button_clicked_msg)
        button.clicked.connect(self.button_clicked_std)
        self.setCentralWidget(button)

    def button_clicked(self, is_checked):
        print("click", is_checked)

        dlg = CusteomDialog(self)
        if dlg.exec():
            print("Success!")
        else:
            print("Cancel")

    def button_clicked_msg(self, is_checked):
        dlg = QMessageBox(self)
        dlg.setWindowTitle("Message Window")
        dlg.setText("This is a message dialog!")

        # Ok, Open, Save, Cancel, Close, Discard, Apply, Reset, RestoreDefaults, Help, SaveAll,
        # Yes, YesToAll, No, NoToAll, Abort, Retry, Ignore, NoButton
        dlg.setStandardButtons(
            QMessageBox.StandardButton.Yes |
            QMessageBox.StandardButton.No
        )

        # NoIcon, Question, Information, Warning, Critical
        dlg.setIcon(QMessageBox.Icon.Information)
        button = dlg.exec()

        button = QMessageBox.StandardButton(button)

        if (button == QMessageBox.StandardButton.Ok):
            print("Ok!")
        elif (button == QMessageBox.StandardButton.Yes):
            print("Yes!")
        else:
            print("No")

    def button_clicked_std(self, is_checked):
        button = QMessageBox.question(
            self, "Info Dialog", "This is an info dialog",
            buttons=QMessageBox.StandardButton.Ok  # Custom button
        )

        if (button == QMessageBox.StandardButton.Yes):
            print("Yes!")
        else:
            print("No!")


if __name__ == "__main__":
    app = QApplication(sys.argv)

    window = MainWindow()
    window.show()

    app.exec()
```

## 参考资料

[十分生动有趣的的 PyQt6 中文教程！](https://drtxdt.github.io/Qt6-book-translation/)
