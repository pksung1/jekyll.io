---
layout: post
title:  "React Native Animated 사용해보기 (1)"
date:   2021-05-06 13:00:00 +0900
categories: animated react-native interactive
---

# React Native Animated

React Native 인터렉티브 개발을 위한 애니메이션 구현방법을 공부하기로 했다.

마침 공부하기 좋은 사이트가 있어 class로 구현된것을 function으로 구현해보며 공부해보기로 했다.


## 애니메이션 속성

### Animated
특정 값 (Opacity, Translate ...)의 세부적인 애니메이션을 만들때 사용합니다.

### LayoutAnimation

create, update 생명주기 시 애니메이션 처리할때 사용됩니다.

Animated로 구현하지 못하는 애니메이션은 LayoutAnimation 으로 구현을 고려해볼수 있습니다.

Android 개발시에는 아래의 코드를 추가해줘야 합니다.
```js
UIManager.setLayoutAnimationEnabledExperimental &&
  UIManager.setLayoutAnimationEnabledExperimental(true);
```

> **이 글에서는 Animated에 대해서 쓰겠습니다.**

## Animated
Animated 는 유연하고 강력하고 편하게 사용할수 있도록 만들었습니다.

inputs값과 outputs 값에 중점을 두고 있습니다.

두 값을 변환하여 사용할수 있고, start/stop 으로 애니메이션 상태를 결정할수도 있습니다.

기본적으로 **Animated.Value** 를 만들어 사용하고 Animated.timing() 함수로 애니메이션을 동작시킵니다.

```jsx
const OpacityScreen = () => {

    // 1
    const anim = useRef(new Animated.Value(0)).current;

    useEffect(() => {
        // 2
        Animated.timing(anim, {
            toValue: 1,
            duration: 3000,
            useNativeDriver: false
        }).start()
    }, [])

    // 3
    return (
        <SafeAreaView>
            <View style={styles.wrapper}>
                <Animated.View style={[styles.box, {opacity: anim}]}></Animated.View>
            </View>
        </SafeAreaView>
    )
}
```

페이지 로드시 가운데 사각형이 3초동안 서서히 나타나는 애니메이션 입니다.

1. Animated.Value 를 생성한다.

ReactNative 문서에는 useRef로 만드는걸 권유하고 있습니다.

변화시킬 애니메이션 값을 생성합니다.
```js
const anim = useRef(new Animated.Value(0)).current;
```

2. 애니메이션을 동작시킵니다.

useEffect훅을 사용해 만들었음으로 컴포넌트가 랜더링이 끝난 시점에 애니메이션을 실행합니다.
```js
Animated.timing(anim, {
    toValue: 1,
    duration: 3000,
    useNativeDriver: false
}).start()
```
0에서 시작해서 1로 이동하는데 3초가 걸린다는 내용입니다.

usenativeDriver는 true로 해주는게 성능은 더 좋아집니다.

자세한 내용은 [문서](https://reactnative.dev/docs/animations#using-the-native-driver)를 참고해주세요.

간단하게는 JS스레드에서 실행되는게 아닌 네이티브 UI 스레드에서 실행할수 있도록 해주는 역할이라고 합니다.

3. 애니메이션 동작 가능한 컴포넌트에 Animated.Value 값을 넣습니다.

그냥 View 에 넣을경우 오류가 발생합니다.

Animated 컴포넌트에 넣어주거나 createAnimatedComponent() 로 생성한 컴포넌트에 아래와 같이 넣어주어야 합니다.
```jsx
<Animated.View style={[styles.box, {opacity: anim}]}></Animated.View>
```

Animated 컴포넌트는 아래와 같이 있습니다.
- Animated.Image
- Animated.ScrollView
- Animated.Text
- Animated.View
- Animated.FlatList
- Animated.SectionList

---
기본적으로 위와같이 사용하고 더 나아가면 Animated 내부 함수로 화려한 애니메이션을 만들수 있습니다.

천천히 하나씩 살펴보겠습니다.


---
### 참고

[React Native Animated Master](https://codedaily.io/courses/Master-React-Native-Animations)

[React Native Animations](https://reactnative.dev/docs/animations)