
+++
title= "Jetpack Compose Dev Challenge Week3で学んだことまとめ"
date= 2021-03-15T12:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose","AndroidDevChallenge"]
keywords = ["Android", "Jetpack", "Compose","AndroidDevChallenge"]
+++

jetpack compose 

## 全体にかかわること(テーマなど)
 
### 背景を入れるには
```kotlin
Surface(color = MaterialTheme.colors.primary) 
```
が大事

## Welcome画面


## Login画面

### Textの途中でリンクを入れる

https://stackoverflow.com/questions/65567412/jetpack-compose-text-hyperlink-some-section-of-the-text


### EditTextの枠線の色を変更するには

outlinedTextFieldColorsの部分

```kotlin
 OutlinedTextField(
    modifier = Modifier
        .height(56.dp)
        .fillMaxWidth(),
    value = "", onValueChange = { /*TODO*/ },
    placeholder = {
        Text(
            text = "Password (8+ characters)",
            style = DevChallengeTheme.typography.body1,
            color = DevChallengeTheme.colors.textBody1,
        )
    },
    colors = outlinedTextFieldColors(
        cursorColor = DevChallengeTheme.colors.textBody1,
        focusedBorderColor = DevChallengeTheme.colors.textBody1.copy(alpha = ContentAlpha.high),
        unfocusedBorderColor = DevChallengeTheme.colors.textBody1,
    ),
    singleLine = true,
)
```

### 検索ボックスを作成（SearchBar)
ここが参考になりそう。
https://github.com/android/compose-samples/blob/main/Jetsnack/app/src/main/java/com/example/jetsnack/ui/home/search/Search.kt

## ホーム画面

###BottomNavigation

https://developer.android.com/jetpack/compose/navigation#bottom-nav
https://proandroiddev.com/implement-bottom-bar-navigation-in-jetpack-compose-b530b1cd9ee2

- BottomItemを切り替えまくっても、バックで、startDestinationに戻る(cart -> Profileと遷移してバックし場合cartに戻っていたのをhomeに戻るようにする)
```kotlin
navController.navigate(screen.route) {
    // BottomItemを切り替えまくっても、バックで、startDestinationに戻る
    popUpTo = navController.graph.startDestination
}
```


### CheckBox

```kotlin
Checkbox(
    checked = checkedState, onCheckedChange = { checkedState = it },
    modifier = Modifier.align(Alignment.CenterVertically),
    colors = CheckboxDefaults.colors(
        uncheckedColor = DevChallengeTheme.colors.secondary,
        checkedColor = DevChallengeTheme.colors.secondary,
        checkmarkColor = DevChallengeTheme.colors.onSecondary,
    )
)
```

###　アイテム要素でいちぶだけ右側に配置

```kotlin
Row(
    horizontalArrangement = Arrangement.SpaceBetween
)
```

### 横方向にリストを作るには

```kotlin
LazyRow(
    content = {
        items(viewModel.themes) { theme ->
            // Cardなど
        }
    }
)
```

### LazyRowのパディングをつけるには

- アイテム間のスペースを開けるには

```kotlin
horizontalArrangement = spacedBy(8.dp),
```

- LazyRowの左右のpaddingを開けるには

```kotlin
contentPadding = PaddingValues(start = 16.dp, end = 16.dp),
```

<img src="/images/2021/03/jetpack-compose-dev-challenge-week3/lazy_row.png" />


### 縦方向にリストを作るには