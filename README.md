# android_summit_2018_note

## [Preferential Practices for Preferences](https://youtu.be/PS9jhuHECEQ)
### androidx.preference  
* API 14 以降  
* いろんな OS バージョンで共通のデザイン  

### SharedPreference
* AppCompatActivity > PreferenceFragmentCompat  
* app になってる  
```
<androidx.preference.PreferenceScreen 
  …>
  
  <Preference
    app:key="version"
    app:title="Build Version"
    app:summery="1.2.345" />
    
  <PreferenceCategory
    app:key="sync"
    app:title="Sync">
    
    <SwitchPreferenceCompat
      app:key="enable_sync"
      app:title="Enable periodic syncing" />
    
  </PreferenceCategory>
  
</androidx.preference.PreferenceScreen>
```

* `EditTextPreference` なんてあったけ?  
* 1.1.0-alpha1 から `useSimpleSummaryProvider` で入力内容を summary に自動で追加  

```
<EditTextPreference
  app:key="code"
  app:title="Code"
  app:useSimpleSummaryProvider="true" />
  
```

* 1.1.0-alpha1 から? `dependency` で `SwitchPreference` とかの key を渡して、 enable を自動で更新  

```
<SwitchPreferenceCompat
  app:key="enable_sync"
  app:title="Enable periodic syncing" />
  
<EditTextPreference
  app:key="code"
  app:title="Code"
  app:dependency="enable_sync" />
```

### Dynamic Summaries  

* 値の変更を監視して summary を動的に変更できる  

```
val syncSummaryProvider = SummaryProvider<SwitchPreferenceCompat> { preference -> 
  if (preference.isChecked) {
    "Checked"
  } else {
    "Non checked"
  }
}

findPreference("enable_sync").summaryProvider = syncSummaryProvider
```

### Multiple screen architecture  

* Activity > Fragment > Hierarchy を Activity > Settings Screen に
* Preference 間の画面遷移が簡単に  

```
<androidx.preference.PreferenceScreen 
  …>
  
  <Preference
    app:key="messages_fragment"
    app:title="Messages"
    app:fragment="com.example.MyMessageFragment" />
  
</androidx.preference.PreferenceScreen>
```


```
class SettingsActovoty : AppCompatActicity(), PreferenceFragmentCompat.OnPreferenceStartFragmentCallback {
  override fun onPreferenceStartFragment(caller: PreferenceFragmentCompat, pref: Preference):Boolean {
    // tansition を独自で実装する場合は true
    // 標準処理にお任せの場合は false
  }
}
```

## [Get Animated](https://youtu.be/N_x7SV3I3P0) [WIP]

* android.view.animation が (Consider)Depereted  
WindowAnimation や Fragment のアニメーションはこれを使用している  

* android.animation を使うことを推奨  

* ObjectAnimator はリフレクション使うからあまりよろしくない

```
// この alpha が問題
ObjectAnimator.ofFloat(view, "alpha", 1f, 0f).apply {
  duration = 100
}.start()

ObjectAnimator.ofFloat(view, View.ALPHA, 1f, 0f).apply {
  duration = 100
}.start()
```

* PropertyValuesHolder  
```
val scaleX = PropertyValuesHolder.ofFloat(View.SCALE_X, 0.5f, 1f)
```

