# Lab 02 — k6 гүйцэтгэлийн хэмжүүр

Нэр: О.Мөнхзолбоо
Оюутны код: B210910878

## 1. Лабораторийн зорилго

Энэхүү лабораторийн ажлын зорилго нь k6 ашиглан веб системийн гүйцэтгэлийг load testing
хийж хэмжих явдал юм. Туршилтыг зөвшөөрөгдсөн дадлагын сайт болох `https://test.k6.io/`
руу хийсэн. k6 тест нь олон virtual user (VU) үүсгэж тухайн сайтад HTTP GET request
илгээж, latency, throughput болон error rate хэмжсэн.

## 2. k6 хувилбар

Туршилтыг дараах k6 хувилбараар гүйцэтгэсэн: 
k6 v2.2.0

## 3. Туршилтын тохиргоо

Үндсэн тест нь 30 секундын турш ажилласан бөгөөд 5, 30, 100 virtual user-ийн ачааллыг тус тусад нь туршсан.
k6 v2.2.0
Target URL:

https://test.k6.io/

## 4. Үр дүнгийн харьцуулалт

Virtual Users	p90 Latency	p95 Latency	Throughput	Error Rate
5 VU	226.87 ms	233.78 ms	7.49 req/s	0.00%
30 VU	222.97 ms	224.53 ms	44.27 req/s	0.00%
100 VU	223.14 ms	226.10 ms	147.20 req/s	0.00%

Бүх туршилтын бүрэн k6 output нь results/ хавтсанд хадгалагдсан.

## 5. SLO болон Threshold

5 VU-ийн baseline туршилтын p95 latency нь 233.78 ms байсан. Үүн дээр 1.5 дахин tolerance нэмж SLO-г тооцсон:

233.78 ms × 1.5 = 350.67 ms

Иймээс үндсэн SLO нь p95 latency 350.67 ms-ээс бага байх, мөн HTTP request-ийн error rate 1%-иас бага байх нөхцөлтэй.

k6 threshold:

thresholds: {
  http_req_duration: ['p(95)<350.67'],
  http_req_failed: ['rate<0.01'],
},

Энэ SLO-г baseline-ийн бодит үр дүн дээр үндэслэн сонгосон тул системийн хэвийн гүйцэтгэлээс хэт хол биш, мөн latency өсөлтийг илрүүлэх боломжтой.

## 6. SLO PASS ба FAIL demonstration

Зөв threshold ашигласан туршилтаар:

p95 = 223.28 ms
SLO = p95 < 350.67 ms
Result = PASS

Мөн SLO failure-г харуулахын тулд p95 threshold-ийг түр хугацаанд 1 ms болгож туршсан. Энэ үед:

p95 = 222.73 ms
Threshold = p95 < 1 ms
Result = FAIL

Error rate хоёр туршилтын үед 0.00% байсан. FAIL demonstration дууссаны дараа зөв SLO болох p(95)<350.67 threshold-ийг буцаан сэргээсэн.

## 7. Дүгнэлт

Энэхүү лабораторийн ажлаар k6 ашиглан системийн performance-ийг 5, 30, 100 VU ачааллын түвшинд туршиж үзсэн. 5 VU үед p95 latency 233.78 ms байсан бол 30 VU үед 224.53 ms, 100 VU үед 226.10 ms байсан. Туршилтын бүх түвшинд error rate 0.00% гарсан. Ачаалал нэмэгдэхэд latency мэдэгдэхүйц өсөөгүй бөгөөд туршилтын хүрээнд тогтвортой байсан. Throughput нь 5 VU үед 7.49 req/s, 30 VU үед 44.27 req/s, 100 VU үед 147.20 req/s болж өссөн. Энэ нь систем нэмэгдсэн request load-ийг туршилтын нөхцөлд амжилттай боловсруулж байгааг харуулж байна. 5 VU baseline-ийн p95 latency дээр үндэслэн 1.5 дахин tolerance нэмж, SLO threshold-ийг 350.67 ms гэж сонгосон. Зөв threshold ашигласан туршилтаар p95 latency 223.28 ms гарч, SLO PASS болсон. Мөн threshold-ийг зориудаар 1 ms болгож өөрчлөн FAIL demonstration хийж, k6 threshold failure-ийг зөв илрүүлж байгааг баталгаажуулсан. Ерөнхийдөө test.k6.io сайт нь 5–100 VU ачааллын хүрээнд 0.00% error rate болон тогтвортой latency үзүүлсэн.

## 8. хүснэгт

Load       	VUs	      p90 Latency	   p95 Latency	  Throughput(req/s)   	Error Rate
Low        	5	        226.87 ms	     233.78 ms	     7.49	                    0.00%
Medium	    30	      222.97 ms	     224.53 ms	     44.27	                  0.00%
High	      100	      223.14 ms	     226.10 ms	     147.20	                  0.00%
