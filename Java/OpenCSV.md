# OpenCSV
.csv 파일 파싱을 위한 Java 라이브러리 

## CSV
```csv
Year,Make,Model
1997,Ford,E350
2000,Mercury,Cougar
```

- CSV(Comma-separated values, 쉼표로 구분된 값) 파일은 파일은 쉼표를 사용하여 값을 구분하는 텍스트 파일이다. 
- 파일의 각 행은 데이터 레코드다. 
- 각 레코드는 쉼표로 구분된 하나 이상의 필드로 구성된다. 
- CSV 파일은 일반적으로 표 형식 데이터를 일반 텍스트로 저장하며, 이 경우 각 행에 동일한 수의 필드가 있다. 

## OpenCSV

### CSV 읽기
```java
List<String[]> list = new ArrayList<>();
CSVReader csvReader = new CSVReader(new FileReader(file));
String[] line;
while ((line = csvReader.readNext()) != null) {
    list.add(line);
}
reader.close();
csvReader.close();
return list;
```

### CSV 쓰기
```java
CSVWriter writer = new CSVWriter(new FileWriter(path.toString()));
for (String[] array : stringArray) {
    writer.writeNext(array);
}

writer.close();
return Helpers.readFile(path);
```

## 참고
https://en.wikipedia.org/wiki/Comma-separated_values  
https://www.baeldung.com/opencsv  
http://opencsv.sourceforge.net/  
https://realrain.net/post/open-csv/  