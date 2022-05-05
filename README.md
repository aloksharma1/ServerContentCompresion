# ServerContentCompresion

```
@ECHO OFF

REG COPY "HKCU\Control Panel\International" "HKCU\Control Panel\International-Temp" /f >nul
REG ADD "HKCU\Control Panel\International" /v sShortDate /d "yyyyMMdd" /f >nul
REG ADD "HKCU\Control Panel\International" /v sTimeFormat /d "HHmmss" /f >nul

FOR /R ".\wwwroot\" %%i IN (*.css,*.txt,*.js) DO (
  IF EXIST %%i.gz (
    FOR %%b IN (%%i.gz) DO (
      IF "%%~ti" GTR "%%~tb" (
        ECHO %%i.gz is older, replacing
        DEL %%i.gz
        timeout /T 2 & start "%%i" /BELOWNORMAL /Min /B c:\zopfli.exe %%i
      )
    )
  ) ELSE (
    ECHO creating %%i.gz
    timeout /T 2 & start "%%i" /BELOWNORMAL /Min /B c:\zopfli.exe %%i
  )
)

REG COPY "HKCU\Control Panel\International-Temp" "HKCU\Control Panel\International" /f >nul
```
