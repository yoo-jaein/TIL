# File validation
파일 검사하기(파일 형식, 용량)

## 파일 형식 검사하기
```javascript
checkFileFormat(evt) {
    let input = evt.target
    if (input.files && input.files[0]) {
        const ext = input.files[0].name.split('.').pop().toLowerCase();
        if (ext != 'csv') {
            this.$toasted.show('파일 형식은 csv만 가능합니다')
            return false
        }
        return true
    }
}
```

```html
<b-form-file accept=".csv" id="fileUpload" ref="file-input" v-model="sendData.students" @change="checkFileType"/>
```

## 파일 용량 검사하기
```javascript
checkFileSize(evt) {
    const file = input.files[0];
    if (file.size > 1024 * 1024 * 9) {
        this.$toasted.show('업로드 가능한 용량(10MB)을 초과했습니다')
        return false
    }
    return true
}
```
