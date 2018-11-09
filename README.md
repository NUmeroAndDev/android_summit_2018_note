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
    // Handle transition anim
  }
}
```

