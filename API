from fastapi import FastAPI
import nest_asyncio
from pyngrok import ngrok
import uvicorn
import requests
from bs4 import BeautifulSoup

app = FastAPI()

def scrape(url):
    #url = 'https://in.indeed.com/jobs?q={question}&l={location}'
    request = requests.get(url).text
    
    soup = BeautifulSoup(request, 'lxml')
    return soup

@app.get('/index')
async def home():
  return "Hello World"

@app.get('/enter-job/{job}')
async def print_jobs(job: str):
  
  question = job.replace(" ","%20")
  def get_url(question):
    return ('https://in.indeed.com/jobs?q={}'.format(question))
  global soup
  soup = scrape(get_url(question))
  cnt = 1
  output = ""
  for a in soup.find_all('div', class_ = 'job_seen_beacon'):
    a = str(a)
    title_start = a.find('title=')
    a = a[title_start:]
    title_end = a.find('>')
    output = output + "Job title: " + str(a[7:title_end-1]) + "\n"
    comp_name_start = a.find('companyName">')
    a = a[comp_name_start:]
    comp_name_end = a.find('<')
    output = output + "Company name: " + str(a[13:comp_name_end]) + "\n"
    comp_loc_start = a.find('companyLocation">')
    a = a[comp_loc_start:]
    comp_loc_end = a.find('<')
    output = output + "Company location: " + str(a[17:comp_loc_end]) + "\n\n"
    cnt+=1
  return output

@app.get('/get-job/{job_no}')
async def print_jobs(job_no: int):
  s = str(soup)
  job_map = s.find('jobmap[{}]'.format(job_no))

  job_url_id = s[job_map+16:job_map+32]
  job_url = 'https://in.indeed.com/viewjob?jk={}'.format(job_url_id)
  job_soup = scrape(job_url)
  return job_soup.find('div', class_ = 'jobsearch-jobDescriptionText').text

ngrok_tunnel = ngrok.connect(8000)
print('Public URL:', ngrok_tunnel.public_url)
nest_asyncio.apply()
uvicorn.run(app, port=8000)
