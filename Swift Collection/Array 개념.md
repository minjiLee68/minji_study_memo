# Array
정렬된 무작위 액세스 컬렉션

배열은 앱에서 가장 일반적으로 사용되는 데이터 유형 중 하나이다. array를 사용하여 앱의 데이터를 정리한다. 

특히, 배열 유형을 사용하여 배열의 요소 유형인 단일 유형의 요소를 보유한다. 배열은 정수에서 문자열, 클래스에 이르기까지 모든 종류의 요소를 저장할 수 있다.

스위프트를 사용하면 배열 리터럴을 사용하여 코드에 배열을 쉽게 만들 수 있다: 쉼표로 구분된 값 목록을 대괄호로 둘러싸기만 하면 된다. 다른 정보가 없으면 Swift는 지정된 값을 포함하는 배열을 생성하여 배열의 요소 유형을 자동으로 추론한다. 예를 들어:

    let oddNumbers = [1, 3, 5, 7, 9, 11, 13, 15]

    let stressts = ["Albemarle", "Brandywine", "Chesapeake"]

선언에서 배열의 요소 유형을 지정하여 빈 배열을 만들 수 있다. 예를 들어:

    단축된 양식 (선호)
    var emptyDoubles: [Double] = []

    전체이름유형도 허용
    var emptyFloats: Array<Float> = Array()

고정된 수의 기본값으로 미리 초기화된 배열이 필요한 경우, Array(repeating:count:) 이니셜라이저를 사용한다.

    var digitCounts = Array(repeating: 0, count: 10)
    print(digitCounts)

    Prints "[0, 0, 0, 0, 0, 0, 0, 0, 0, 0]"

## 배열 값에 접근하기
배열의 모든 요소에 대한 작업을 수행해야 할 때, for-in 루프를 사용하여 배열의 내용을 반복하면된다.

    for street in streets {
        code...
    }

isEmpty 속성을 사용하여 배열에 요소가 있는지 빠르게 확인하거나 count 속성을 사용하여 배열의 요소 수를 찾을 수 있다.

    if oddNumbers.isEmpty {
        code..
    } else {
        print(oddNumbers.count)
    }

개별 배열 요소에 액세스할 수 있다. 비어 있지 않은 배열의 첫 번째 요소는 항상 인덱스 0에 있다. 배열의 수를 포함하지 않는 0에서 정수로 배열을 하위 스크립트로 만들 수 있다. 음수나 count와 같거나 큰 인덱스를 사용하면 런타임 오류가 발생한다.

    print(oddNumbers[0], addNumbers[3], separator: ", ")

    print "1, 7

## 요소 추가 및 제거
강의 중인 수업에 등록한 학생들의 이름 목록을 저장해야 한다고 가정해 보자. 등록 기간 동안, 학생들이 수업을 추가하고 취소할 때 이름을 추가하고 제거해야 한다.

    var students = ["Ben", Ivy", "Jordell"]

 배열 끝에 단일 요소를 추가하려면, append(_:) 메서드를 사용한다. 다른 배열이나 모든 종류의 시퀀스를 append(contentsOf:) 메서드에 전달하여 동시에 여러 요소를 추가할 수 있다.

    students.append("Maxime")
    students.append(contentsOf: ["Shakia", "William"])

    ["Ben", "Ivy", "Jordell","Maxime", "Shakia", "William"]   

단일 요소에 대한 insert(_:at:) 메서드를 사용하고 insert(contentsOf:at:)를 사용하여 다른 컬렉션이나 배열 리터럴의 여러 요소를 삽입하여 배열 중간에 새 요소를 추가할 수 있다. 그 인덱스와 이후 인덱스의 요소는 공간을 만들기 위해 다시 이동된다.

    students.insert("Liam", at: 3)

    ["Ben", "Ivy", "Jordell", "Liam", "Maxime", "Shakia", "William"]

배열에서 요소를 제거하려면 remove(at:), removeSubrange(_:) 및 removeLast() 메서드를 사용한다.

배열을 복사할 수 있지만 값은 독립적으로 움직인다.

    var a = [1,2,3,4]
    var b = a
    a[0] = 10

    a -> [10,2,3,4]
    b -> [1,2,3,4]
