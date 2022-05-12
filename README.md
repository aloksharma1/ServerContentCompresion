# ServerContentCompresion

the following is a batch script for windows server to compress files using zopfli,pngzopfli or brotli based on their last modified dates.
you can simply change extension types & compression provider as per needs. this can also be turned into a windows services that can run on a specific path daily and keep new files compressed on server itself. 

```
@ECHO OFF

REG COPY "HKCU\Control Panel\International" "HKCU\Control Panel\International-Temp" /f >nul
REG ADD "HKCU\Control Panel\International" /v sShortDate /d "yyyyMMdd" /f >nul
REG ADD "HKCU\Control Panel\International" /v sTimeFormat /d "HHmmss" /f >nul

FOR /R ".\wwwroot\" %%i IN (*.css,*.js,*.html,*.json,*.ico,*.eot,*.otf,*.ttf,*.xml,*.txt,*.svg) DO (
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
