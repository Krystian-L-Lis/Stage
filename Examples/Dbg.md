```
FUNCTION_BLOCK Example IMPLEMENTS I_Debug
  METHOD ToDebug : I_Debug
    ToDebug := THIS^;
  END_METHOD
END_FUNCTION_BLOCK

PROGRAM MAIN
  VAR
    example : Example;
    debugList: DebugList;
    bInit : BOOL := TRUE;
  END_VAR
  
  IF bInit THEN
    DebugMsg(example, ': Example message!');
    bInit := FALSE;
  END_IF
END_PROGRAM
```

![Debug Result](main/Examples/Images/DebugResult.png)
