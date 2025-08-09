---
layout: single
title: "MVC... 네가 뭔데 날 울려"
categories:
  - personal
sidebar:
  nav: "sidebar-category"
---

우테코 1주차를 하면서 DTO, MVC, TDD 등... 너무 어려운 패턴 등장의 연속이였다.<br />
그중 MVC는 이전에 리액트+노제로 공부했던 구조랑 비슷하길래 (사실 그 때 배웠는지도 모른다... 백엔드 어렵다고 손놓은 내 죄.. )

```
Model : 데이터와 관련된 부분
View : 사용자에게 보여지는 부분
Controller : Model과 View를 이어주는 부분
```

아래 다섯 가지를 중점으로 생각하면 된다.
- Model 내부에 Controller와 View에 관련된 코드가 있으면 안 된다.
- View 내부에 Model의 코드만 있을 수 있고, Controller의 코드가 있으면 안 된다.
- View가 Model로부터 데이터를 받을 때는, 사용자마다 다르게 보여주어야 하는 데이터에 대해서만 받아야 한다.
- Controller내부에는 Model과 View의 코드가 있을 수 있다.
- View가 Model로부터 데이터를 받을 때, 반드시 Controller에서 받아야 한다.
그래 뭔가 어떻게 돌려야 하는지는 얼추 알 것 같다. 근데 문제는 폴더구조나, 구현방법 등에 대해서 아무것도 모른다는 거였다...<br />
그래서 간단한 예제를 직접 코드를 구현해보면서 이해해보려 했다.

### Model

간단하게 변수 정해주고 해당 변수에 대한 getter, setter 생성한다. (Controller에서 사용가능)

``` java 
public class Student {
    private String rollNo;
    private String name;

    public String getRollNo() {
        return rollNo;
    }

    public void setRollNo(String rollNo) {
        this.rollNo = rollNo;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

### View

View는 정말 보여줄 틀만 정의해준다. (Controller에서 사용가능)

``` java 
public class StudentView {
    public void printStudentDetails(String studName, String studentRollNum) {
        System.out.println("Student Details");
        System.out.println("Name: " + studName);
        System.out.println("Roll Number: " + studentRollNum);
    }
}
```

### Controller 

일단 Model과 View의 객체를 생성한다.<br />
Model를 컨트롤할 수 있는 getter, setter를 생성한다. (Model에서 생성한 getter, setter 사용) <br />
View를 위에서 생성한 메서드를 이용해서 보여줄 수 있게 생성한다.

``` java 
public class StudentController {
    // Model Object
    private Student model;
    // View Object
    private StudentView view;

    public StudentController(Student model, StudentView view) {
        this.model = model;
        this.view = view;
    }

    // Control model object
    public void setStudentName(String name) {
        model.setName(name);
    }

    public void setStudentRollNum(String rollNo) {
        model.setRollNo(rollNo);
    }

    public String getStudentName() {
        return model.getName(); 
    }

    public String getStudentRollNum() {
        return model.getRollNo();
    }

    // Control view object
    public void updateView() {
        view.printStudentDetails(model.getName(), model.getRollNo());
    }
}
```

### Main

Model의 세터를 이용해서 정보 저장 후, Controller를 이용해서 View에 전달

``` java 
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Student model = retrieveStudentFromDatabase(); // 저장한 데이터 불러오기
        StudentView view = new StudentView();
        StudentController controller = new StudentController(model, view);
	
    	// Controller를 이용해서 Model에 저장한 데이터를 View에 전달
        controller.updateView();;
        controller.setStudentRollNum("4567");
        controller.updateView();;
    }

    // Model에 데이터 저장
    private static Student retrieveStudentFromDatabase() {
        Student student = new Student();
        student.setName("Kim");
        student.setRollNo("1234");
        return student;
    }
}
```

컨트롤러에서 모델, 뷰를 제어할 메서드를 정의, 모델/뷰에서 정의한 메서드 컨드롤러에서 써도됨<br />
뷰에다가 모델에서 정보를 주려면 컨트롤러에 정의 후에 줘야됨<br />
구현을 해보니까 확실히 이해는 되는데... 이 안에서도 메서드와 클래스를 나눌 걸 생각하니까 머리 터지겠다! 하지만 해야지...

[참고 영상 1](https://www.youtube.com/watch?v=ogaXW6KPc8I&t=4s)<br />
[참고 영상 2](https://www.youtube.com/watch?v=FYOMpzia_Dk)