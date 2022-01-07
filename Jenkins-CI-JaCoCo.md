# Jenkins-CI-JaCoCo

Jenkins 에 JaCoCo 플러그인을 설치하면 빌드 시 테스트를 돌리고 코드 커버리지 결과 리포트를 볼 수 있다.

Jenkins 플러그인은 단지 결과 리포트를 보여주는 역할을 하며, 결과 리포트를 생성하기 위해서는 먼저 빌드 과정에서 JaCoCo 가 커버리지 검사를 실행하도록 태스크를 설정해줘야 한다.

## Gradle 에 JaCoCo 설정

개략적인 샘플은 다음과 같고 자세한 내용은 https://docs.gradle.org/current/userguide/jacoco_plugin.html 참고

```kotlin
// build.gradle.kts

//...
plugins {
    //...
    id("jacoco")
    //...
}

//...

tasks.withType<Test> {
    useJUnitPlatform()
    finalizedBy("jacocoTestReport")  // 추가!!
}

// 아래 내용도 추가!!

jacoco {
    toolVersion = "0.8.7"
}

tasks.jacocoTestReport {
    dependsOn("test")
    reports {
        html.isEnabled = true
        xml.isEnabled = true
        csv.isEnabled = true
    }
    finalizedBy("jacocoTestCoverageVerification")
}

tasks.jacocoTestCoverageVerification {
    violationRules {
        rule {  // element 를 지정하지 않으면 프로젝트 전체 대상
            limit {
                minimum = "0.10".toBigDecimal()
            }
        }
        rule {
            element = "CLASS"
            enabled = true
            limit {  // 클래스에서 라인 수 기준 커버리지가 minimum 이하면 빌드 실패 처리
                counter = "LINE"
                value = "COVEREDRATIO"
                minimum = "0.00".toBigDecimal()
            }
            limit {  // 클래스에서 메서드 수 기준 커버리지가 minimum 이하면 빌드 실패 처리
                counter = "METHOD"
                value = "COVEREDRATIO"
                minimum = "0.00".toBigDecimal()
            }
        }
    }
}

}
//...
```


## JUnit 플러그인 설치

Jenkins > Plugin Manager 에서 JaCoCo 플러그인 설치

## Job 설정

### 테스트 실행 지정

- Build 항목에 테스트 실행 태스크를 추가, 아래는 gradle 사례

  - ![Imgur](https://i.imgur.com/Y0CJDxk.png)

### 빌드 후 조치 추가

- 테스트 결과 xml 파일을 읽어서 보여줄 수 있도록 개별 Job > 구성 > 빌드 후 조치 > 빌드 후 조치 추가 > 'Record JaCoCo coverage report' 항목 추가

  - ![Imgur](https://i.imgur.com/BJrB42d.png)

- 클래스 위치, 소스 파일 위치, 코드 커버리지 기준값 등 설정


## 테스트 결과 확인

- 빌드 후 Build History 에 아래와 같이 'Coverage Result' 메뉴가 표시된다.

  - ![Imgur](https://i.imgur.com/AgsMWuy.png)

- 클릭하면 아래와 같이 리포트를 볼 수 있다.

  - ![Imgur](https://i.imgur.com/3RLWwsu.png)

- 참고로 gradle 에 JaCoCo 태스크 설정을 안 해도 Jenkins 에서 리포트가 생성되긴 하는데, 테스트 코드에 의해 코드 커버리지 측정이 안 되었으므로 모두 빨간색으로만 표시된다.

  - ![Imgur](https://i.imgur.com/aAz56O2.png)

- Job 콘솔 로그를 보면 아래와 같이 JaCoCo 관련 로그가 표시된다

  - ![Imgur](https://i.imgur.com/iMOBQ9t.png)


