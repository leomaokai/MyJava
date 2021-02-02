```java
DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");

String DateStr="2021-02-02";
LocalDate parse = LocalDate.parse(DateStr, timeFormatter);
System.out.println(parse);
// 2021-02-02

LocalDateTime now = LocalDateTime.now();
System.out.println(now);
// 2021-02-02T12:12:42.251607400
String format = now.format(timeFormatter);
System.out.println(format);
// 2021-02-02

// simpleDateFormat线程不安全
Date date = new Date();
System.out.println(date);
// Tue Feb 02 12:12:42 CST 2021
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
String format1 = simpleDateFormat.format(date);
System.out.println(format1);
// 2021-02-02
```

