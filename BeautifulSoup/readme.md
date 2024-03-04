# 1. Đọc file html lấy text
```
with open("file.html", 'r') as html_file:
	content = html_file.read()
	soup = BeautifulSoup(content, 'lxml')
	course_tag = soup.find_all('h5') # Lấy tất cả các thẻ h5
	for course in course_tag:
		print(course.text) # Lấy text ở trong thẻ h5
```

# 2. Đọc request từ website

```
html_text = request.get(url)
soup = BeautifulSoup(html_text, 'lxml')
job = soup.find_all('li', class_='clearfix job-bx wht')
for job in jobs:
	published_date = job.find('h3', class_='sim-posted').span.text
	
	if 'few' in published_date:
		company_name = job.find('h3',class_='joblist-comp-name').text
		skill = job.find('span',class_='srp-skill').text.strip()
		more_info = job.header.h2.a['href']
```
