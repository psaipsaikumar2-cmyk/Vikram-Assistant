cd ~/VikramApp

for f in \
app/build.gradle \
build.gradle \
settings.gradle \
app/src/main/AndroidManifest.xml \
app/src/main/java/com/vikram/ai/MainActivity.kt \
app/src/main/res/values/styles.xml
do
    echo
    echo "========== $f =========="
    cat "$f"
done
cd ~/VikramApp
find . -type f \
! -path './.gradle/*' \
! -path './build/*' \
! -path './app/build/*' \
! -name '*.apk' | sort