# sql-challenge

## [The PADS](https://www.hackerrank.com/challenges/the-pads/) (medium)
```sql
select concat(name, '(', substring(occupation, 1, 1), ')') as new_name
from occupations
order by new_name;
select concat('There are a total of ', cnt , ' ', occ, 's.')
from (select convert(count(*), char) as cnt, lower(occupation) as occ
     from occupations
     group by occupation
     order by cnt, occ) as new_table;
```
- Explain: Bài toán chỉ yêu cầu xử lý chuỗi cơ bản kèm theo hàm count.

## [Binary Tree Nodes](https://www.hackerrank.com/challenges/binary-search-tree-1) (medium)
```sql
select n, 'Leaf'
from bst as bi
where (select count(p) from bst where p = bi.n) = 0
union
select n, 'Root'
from bst as bi
where p is null
union
select n, 'Inner'
from bst as bi
where (select count(p) from bst where p = bi.n) > 0 and bi.p is not null
order by n;
```
- Explain: Bài toán yêu cầu phân loại các đỉnh của cây dựa trên định nghĩa. Khi đó ta có thể chia thành ba phần
  - Các đỉnh lá là các đỉnh mà không có đỉnh nào nhận nó làm đỉnh cha.
  - Gốc của cây là đỉnh mà đỉnh cha của nó là null.
  - Các đỉnh còn lại.
## [New Companies](https://www.hackerrank.com/challenges/the-company) (medium)
```sql
select c.company_code, c.founder, count(distinct l.lead_manager_code), count(distinct s.senior_manager_code), count(distinct m.manager_code), count(distinct e.employee_code)
from company c
join lead_manager l on c.company_code = l.company_code
join senior_manager s on s.company_code = l.company_code and s.lead_manager_code = l.lead_manager_code
join manager m on m.company_code = s.company_code and m.lead_manager_code = s.lead_manager_code and m.senior_manager_code = s.senior_manager_code
join employee e on e.company_code = m.company_code and e.lead_manager_code = m.lead_manager_code and e.senior_manager_code = m.senior_manager_code and e.manager_code = m.manager_code
group by c.company_code, c.founder
order by c.company_code;
```
- Explain: Ta chỉ cần join các bản và dùng hàm count.

## [Weather Observation Station 20](https://www.hackerrank.com/challenges/weather-observation-station-20) (medium)
```sql
select round(lat_n, 4)
from station s 
where (select count(*) from station where lat_n < s.lat_n) = (select count(*) from station where lat_n > s.lat_n);
```
- Explain: Bài toán yêu cầu tìm trung vị, ở đây đầu tiên ta có thể dùng 1 lệnh select count() để xem có bao nhiêu hàng,
ta để ý rằng số hàng đã cho là lẻ nên ta có thể tìm trung vị là vị trí có số lượng giá trị bé hơn nó = vị trí có số lượng lớn hơn nó.
- Ngoài ra ta có thể làm như sau:
```sql
select val
from (select row_number() over () as id, round(lat_n, 4) as val
from station
order by lat_n) t
where t.id = 250
```
-Explain: Ta có thể sắp xếp các giá trị theo thứ tự tăng dần và đánh thứ tự. Sau đó chọn lấy phần tử chính giữa (ở đây là 250)
Với cách này, ta có thể mở rộng cho trường hợp số lượng số hạng là chẵn.

## [Weather Observation Station 18](https://www.hackerrank.com/challenges/weather-observation-station-18) (medium)
```sql
select round((select max(lat_n) from station) - (select min(lat_n) from station)
+ (select max(long_w) from station) - (select min(long_w) from station), 4)
from station
limit 1
```
-Explain: Bài toán chỉ yêu cầu tính khoảng cách Mahattan giữa 2 điểm cho sẵn.

## [Weather Observation Station 19](https://www.hackerrank.com/challenges/weather-observation-station-19) (medium)

```sql
select round(sqrt(power((select max(lat_n) from station) - (select min(lat_n) from station), 2)
                 + power((select max(long_w) from station) - (select min(long_w) from station), 2)), 4)
                 from station
                 limit 1;
```
- Explain: Giống bài trên tuy nhiên yêu cầu tính khoảng cách Euclidean.
## [The Report](https://www.hackerrank.com/challenges/the-report) (medium)
```sql
select s.name as student_name, (select grade from grades where s.marks between min_mark and max_mark) as score, s.marks as real_score
from students s
where s.marks >= 70
order by score desc, student_name;
 
select 'NULL' as student_name, (select grade from grades where s.marks between min_mark and max_mark) as score, s.marks as real_score
from students s
where s.marks < 70
order by score desc, real_score;
```
- Explain: Ta chỉ cần xác định grade phù hợp cho mỗi thí sinh, sau đó in thông tin theo yêu cầu. Để ý rằng với những học sinh
có điểm dưới 70 thì tên sẽ bị thay bằng NULL.
## [Top Competitors](https://www.hackerrank.com/challenges/full-score) (medium)
```sql
select id, name
from (select id, name, count(*) as wins
from (select h.hacker_id as id, h.name as name, s.challenge_id as c_id, max(s.score) as score
from hackers h
join submissions s on h.hacker_id = s.hacker_id
group by h.hacker_id, h.name, s.challenge_id) t1
where score = (select score from challenges join difficulty using (difficulty_level) where challenge_id = c_id)
group by id, name
order by wins desc, id) t2
where wins > 1;
```
- Explain: Trong bài này, mỗi người có thể submit nhiều lần cho 1 contest, do đó ta chỉ lấy điểm cao nhất cho mỗi contest
đối với từng người. Việc còn lại ta chỉ cần kiểm tra xem với lượng điểm đó thì người đó có đạt full score hay không. Cuối cùng ta 
chỉ cần in ra những người đạt full score nhiều hơn 1 contest.
## [Ollivander's Inventory](https://www.hackerrank.com/challenges/harry-potter-and-wands) (medium)
```sql
select w.id, p.age, w.coins_needed, w.power
from wands w
inner join wands_property p on w.code = p.code
where is_evil = 0 and w.coins_needed = (select min(coins_needed)
                                       from wands w1
                                       join wands_property p1 on w1.code = p1.code
                                       where w1.power = w.power and p1.age = p.age)
order by w.power desc, p.age desc, w.coins_needed
```
- Explain: Đề bài yêu cầu in ra những cây đũa phép mà Ron có thể thích, tuy nhiên ở đây **đề bài bị thiếu mất 1 phần:
những cây đũa phép được in ra sẽ phải là cây đũa phép rẻ nhất trong số những cây đũa phép có cùng age và power**.
Điều này được chỉ ra bởi 1 số bình luận ở mục discussion.
## [Challenges](https://www.hackerrank.com/challenges/challenges) (medium)
```sql
select t.id, t.name , t.created
from (select h.hacker_id as id, h.name as name, count(*) as created
from hackers h
join challenges c on h.hacker_id = c.hacker_id
group by h.hacker_id, h.name
order by created desc, h.hacker_id) t
where t.created = (select max(t1.created) from (select h.hacker_id as id, h.name as name, count(*) as created
from hackers h
join challenges c on h.hacker_id = c.hacker_id
group by h.hacker_id, h.name
order by created desc, h.hacker_id) t1)
or not exists (select t2.name from (select h.hacker_id as id, h.name as name, count(*) as created
from hackers h
join challenges c on h.hacker_id = c.hacker_id
group by h.hacker_id, h.name
order by created desc, h.hacker_id) t2 where t2.created = t.created and t2.name != t.name)
order by t.created desc, t.id;
```
- Explain: Ta chỉ cần in ra những người tạo nhiều contest nhất hoặc những người có **số lượng contest là duy nhất**.
Để kiểm tra số lượng contest tạo ra bởi 1 người nào đó có duy nhất hay không ta chỉ cần kiểm tra xem có ai đó khác
tạo ra số lượng contest tương tự hay không. Đây là 1 câu không khó nhưng câu lệnh truy vấn khá loằng ngoằng.

## [Contest Leaderboard](https://www.hackerrank.com/challenges/contest-leaderboard) (medium)
```sql
select id, name, sum(score) as total_score
from (select h.hacker_id as id, h.name as name, s.challenge_id as c_id, max(s.score) as score
from hackers h
join submissions s on h.hacker_id = s.hacker_id
group by h.hacker_id, h.name, s.challenge_id) t
group by id, name
having total_score > 0
order by total_score desc, id;
```
- Explain: Đầu tiên do 1 người có thể submit nhiều lần cho 1 challenge nên ta sẽ chọn submission có điểm cao nhất.
Tiếp đó ta chỉ cần sum tất cả các challenge mà người đó tham gia, cuối cùng ta sẽ loại bỏ những người có tổng điểm là 0.

## [Print Prime Number](https://www.hackerrank.com/challenges/print-prime-numbers) (medium)
```sql
select string_agg(t.val, '&')
from (SELECT (ones.n + 10*tens.n + 100*hundreds.n) as val
FROM (VALUES(0),(1),(2),(3),(4),(5),(6),(7),(8),(9)) ones(n),
     (VALUES(0),(1),(2),(3),(4),(5),(6),(7),(8),(9)) tens(n),
     (VALUES(0),(1),(2),(3),(4),(5),(6),(7),(8),(9)) hundreds(n)
      order by val offset 0 rows
) t
where t.val > 1 and not exists (select val from (SELECT (ones.n + 10*tens.n + 100*hundreds.n) as val
FROM (VALUES(0),(1),(2),(3),(4),(5),(6),(7),(8),(9)) ones(n),
     (VALUES(0),(1),(2),(3),(4),(5),(6),(7),(8),(9)) tens(n),
     (VALUES(0),(1),(2),(3),(4),(5),(6),(7),(8),(9)) hundreds(n)
      order by val offset 0 rows) t2 where t2.val > 1 and t2.val < t.val and (t.val % t2.val) = 0);
```
- Explain: Đầu tiên ta tìm cách sinh tất cả các số từ 0 - 1000. Sau đó với mỗi số ta kiếm tra xem nó có chia 
hết cho số nào trong khoảng từ 2 đến số đó trừ đi 1 hay không.
- Ta có thể làm bài toán trên bằng cách sử dụng 1 ngôn ngữ khác (chẳng hạn Python) sinh ra kết quả, sau đó thực hiện truy vấn:
```sql
select '2&3&5&7&11&13&17&19&23&29&31&37&41&43&47&53&59&61&67&71&73&79&83&89&97&101&103&107&109&113&127&131&137&139&149&151&157&163&167&173&179&181&191&193&197&199&211&223&227&229&233&239&241&251&257&263&269&271&277&281&283&293&307&311&313&317&331&337&347&349&353&359&367&373&379&383&389&397&401&409&419&421&431&433&439&443&449&457&461&463&467&479&487&491&499&503&509&521&523&541&547&557&563&569&571&577&587&593&599&601&607&613&617&619&631&641&643&647&653&659&661&673&677&683&691&701&709&719&727&733&739&743&751&757&761&769&773&787&797&809&811&821&823&827&829&839&853&857&859&863&877&881&883&887&907&911&919&929&937&941&947&953&967&971&977&983&991&997';
```
- Tuy nhiên với cách này thì có vẻ không được "chính đạo" cho lắm.
## [SQL Project Planning](https://www.hackerrank.com/challenges/sql-projects) (medium)
```sql
select start_date, min(end_date) as finished_date
from (select start_date from projects where start_date not in (select end_date from projects)) a,
(select end_date from projects where end_date not in (select start_date from projects)) b
where start_date < end_date
group by start_date
order by datediff(start_date, min(end_date)) desc, start_date;
```
- Explain: Ta thấy rằng những đoạn thời gian nằm liên tục nhau sẽ cùng thuộc 1 dự án, do đó nếu start_date của 1 đoạn
thoài gian nào đó là end_date của 1 đoạn khác thì chắc chắn nó sẽ nằm trong cùng 1 dự án, và ta có thể bỏ qua giá trị start_date này.
Tương tự cho end_date. Khi đó ta có thể select start_date và min(end_date) trong 2 danh sách trên sao cho end_date > start_date là
được.

## [Symetric Pairs](https://www.hackerrank.com/challenges/symmetric-pairs) (medium)
```sql
select distinct x, y
from functions f
where (x = y and (select count(f1.x) from functions f1 where f1.x = f1.y and f1.x = f.x ) > 1)
or (exists(select f3.x, f3.y from functions f3 where f3.x = f.y and f3.y = f.x) and x < y)
order by x;
```
- Explain: ta chia làm 2 trường hợp:
  - x = y: Khi đó ta sẽ đếm xem có bao nhiêu cặp có x = y, nếu có nhiều hơn 1 thì ta select cặp này.
  - x < y: khi đó ta chỉ kiểm tra liệu có tồn tại cặp (y, x) hay không.

## [15 Days of Learning SQL](https://www.hackerrank.com/challenges/15-days-of-learning-sql) (hard)
```
select submission_date, 
(select count(distinct hacker_id)
from submissions s2
where (select count(distinct submission_date) from submissions s3 where s3.submission_date < s1.submission_date and s3.hacker_id = s2.hacker_id) = datediff(s1.submission_date, '2016-03-01')
 and s2.submission_date = s1.submission_date
),
(select hacker_id from submissions s2 where s2.submission_date = s1.submission_date
 group by hacker_id
 order by count(submission_id) desc, hacker_id
 limit 1
) as most_active_hacker,
(select name from hackers where hacker_id = most_active_hacker)
from (select distinct submission_date from submissions) s1
order by submission_date
```
- Explain: Lại 1 bài nữa đề bài không phát biểu đúng những gì cần làm. Ở đây ứng với mỗi ngày, ta sẽ phải in ra
số lượng người đã submit liên tục ít nhất 1 lần trong mỗi ngày từ ngày 01-03 đến ngày đang xét, tiếp theo ta sẽ in
ra id và name của người có số lần submit cao nhất trong ngày đó (không quan tâm đến việc người đó có submit những 
ngày trước đó hay không).

## [Occupations](https://www.hackerrank.com/challenges/occupations) (medium)
```sql
select d, p, s, a
from
(select row_number() over () as id, d
from
((select name as d, 1 as val from occupations where occupation = 'Doctor')
        union all (select 'NULL' as d, 2 as val from occupations limit 4) order by val, d) doctors) t1
join (select row_number() over () as id, name as p from occupations where occupation = 'Professor' order by p) t2
using (id)
join 
(select row_number() over () as id, s
from
((select name as s, 1 as val from occupations where occupation = 'Singer')
        union all (select 'NULL' as s, 2 as val from occupations limit 3) order by val, s) singers) t3
using (id)
join 
(select row_number() over () as id, a
from
((select name as a, 1 as val from occupations where occupation = 'Actor')
        union all (select 'NULL' as a, 2 as val from occupations limit 3) order by val, a) actors) t4
using (id);
```
- Explain: Đầu tiên ta select count group by occupation để xem ứng với mỗi nghề nghiệp ta sẽ có tối đa bao nhiêu giá trị.
Ở đây ta có số giá trị là 7, khi đó ta sẽ tiền hành thêm NULL vào những nghề không đủ 7 giá trị. Để có thể join các bảng row by row
ta sẽ phải thêm id cho mỗi hàng => Ở đây ta sẽ cần 1 phương án tốt hơn bởi có quá nhiều "magic number"

## [Placements](https://www.hackerrank.com/challenges/placements) (medium)
```sql
select name
from (select distinct name, p1.salary as v1, p2.salary as v2
from students s1
join packages p1 using(id),
friends f2 
join packages p2
on f2.friend_id = p2.id
where s1.id = f2.id and p2.salary > p1.salary
order by p2.salary) t;
```
- Explain: Ta chỉ cần tìm những người có bạn thân được offer lương cao hơn, sau đó tiền hành sắp xếp và select name.
