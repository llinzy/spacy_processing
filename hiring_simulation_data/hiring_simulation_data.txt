#!/usr/bin/env python
# coding: utf-8

#employee:608673


import pandas as pd
import warnings
warnings.filterwarnings("ignore")
import time
import re
import numpy as np
import pandas as pd
import random
from random import seed
from numpy.random import randint
import time
import datetime
import itertools
import numpy as np
import re
import ast
full_start=time.perf_counter()
seed(2) #remove seed if you want a new set of data with different values
print('\n\nGenerating data...\n\n')
race=['black','white','asian','native american','hispanic','native hawaiian']
gender=['female','male','other']

#race & gender columns
start=datetime.datetime(2019,8,1,0,0).timestamp()
end=datetime.datetime(2020,4,30,0,0).timestamp()
#num_days=datetime.datetime(2020,4,30,0,0)-datetime.datetime(2019,8,1,0,0)
#total_days=num_days*150
#one day = 86400 in epoch time
total_days=int((end-start)/86400)
apps_per_day=150
total_data_len=total_days*apps_per_day
race_data=race*int(total_data_len/len(race))
random.shuffle(race_data)
gender_data=gender*int(total_data_len/len(gender))
random.shuffle(gender_data)

new_data=pd.concat([pd.Series(race_data), pd.Series(gender_data)], axis=1)

date_range=list(range(1564632000,1588219200))
date_range_273=np.linspace(1564632000,1588219200, 273)
date_range_273=[int(i) for i in date_range_273]

dates=[] 
for i in date_range_273:
    
    dates.append(time.strftime("%Y-%m-%d", time.localtime(i)))
    
application_date=[list(itertools.repeat(i, 150)) for i in dates]
application_date=list(itertools.chain.from_iterable(application_date))
split_app_data=int(len(application_date)/5)

equal_parts=[application_date[i:i + split_app_data] for i in range(0,len(application_date),split_app_data)]

file=open('holiday_weekends.txt', 'r')
content=file.read()

holiday2_weekends=ast.literal_eval(content)
file.close

aa5=[]
for i in equal_parts[0]:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=5)
    aa5.append(new_date.strftime("%Y-%m-%d"))

aa6=[]
for i in equal_parts[1]:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=6)
    aa5.append(new_date.strftime("%Y-%m-%d"))

aa7=[]
for i in equal_parts[2]:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=7)
    aa7.append(new_date.strftime("%Y-%m-%d"))
    
aa8=[]
for i in equal_parts[3]:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=8)
    aa8.append(new_date.strftime("%Y-%m-%d"))
    
aa9=[]
for i in equal_parts[4]:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=9)
    aa9.append(new_date.strftime("%Y-%m-%d"))    

application_recieved=aa5+aa6+aa7+aa8+aa9

new_data['application_date']=pd.Series(application_date)
new_data['application_recieved']=pd.Series(application_recieved)
new_data.columns=['race', 'gender', 'application_date','application_recieved']

new_data=new_data.sort_values('application_recieved')
new_data=new_data.reset_index()

new_data['application_recieved'].replace(holiday2_weekends,inplace=True)

#application review-15days
#rule-6 person staff
b4_thanksgiving=new_data[new_data['application_recieved']<'2019-11-20']
#rule=3 person staff
thanksgiving_holidays=new_data[(new_data['application_recieved']>='2019-11-20') & (new_data['application_recieved']<'2019-12-01')]
#rule-6 person staff
b4_christmas=new_data[(new_data['application_recieved']>='2019-12-01') & (new_data['application_recieved']<'2019-12-21')]
#rule=3 person staff
christmas_holidays=new_data[(new_data['application_recieved']>='2019-12-21') & (new_data['application_recieved']<'2020-01-02')]
#rule-6 person staff
b4_covid19=new_data[(new_data['application_recieved']>='2020-01-02') & (new_data['application_recieved']<'2020-03-17')]
#rule-2 person staff
during_covid19=new_data[new_data['application_recieved']>='2020-03-17']

def equal_parts(data):
    #split groups into forths
    equal_parts2=int(round(len(data)/4,0))
    r5=data[:equal_parts2]
    r6=data[equal_parts2+1:equal_parts2*2+1]
    r7=data[equal_parts2*2+1:equal_parts2*3+1]
    r8=data[equal_parts2*3:]

    return pd.DataFrame(r5),pd.DataFrame(r6),pd.DataFrame(r7),pd.DataFrame(r8)

b4_thanksgiving_split=equal_parts(b4_thanksgiving)
thanksgiving_holidays_split=equal_parts(thanksgiving_holidays)
b4_christmas_split=equal_parts(b4_christmas)
christmas_holidays_split=equal_parts(christmas_holidays)
b4_covid19_split=equal_parts(b4_covid19)
during_covid19_split=equal_parts(during_covid19)

def hr_date_generator_nonholiday(part1,part2,part3, part4):
    aa1=[]
    for i in part1:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=9)
        aa1.append(new_date.strftime("%Y-%m-%d"))
    aa2=[]
    for i in part2:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=10)
        aa2.append(new_date.strftime("%Y-%m-%d"))
    aa3=[]
    for i in part3:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=12)
        aa3.append(new_date.strftime("%Y-%m-%d"))
    aa4=[]
    for i in part4:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=13)
        aa4.append(new_date.strftime("%Y-%m-%d"))
    return pd.Series(aa1+aa2+aa3+aa4)

def hr_date_generator_holiday(part1,part2,part3, part4):
    aa1=[]
    for i in part1:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=14)
        aa1.append(new_date.strftime("%Y-%m-%d"))
    aa2=[]
    for i in part2:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=15)
        aa2.append(new_date.strftime("%Y-%m-%d"))
    aa3=[]
    for i in part3:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=17)
        aa3.append(new_date.strftime("%Y-%m-%d"))
    aa4=[]
    for i in part4:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=18)
        aa4.append(new_date.strftime("%Y-%m-%d"))
    return pd.Series(aa1+aa2+aa3+aa4)

def hr_date_generator_covid19(part1,part2,part3, part4):
    aa1=[]
    for i in part1:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=19)
        aa1.append(new_date.strftime("%Y-%m-%d"))
    aa2=[]
    for i in part2:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=20)
        aa2.append(new_date.strftime("%Y-%m-%d"))
    aa3=[]
    for i in part3:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=22)
        aa3.append(new_date.strftime("%Y-%m-%d"))
    aa4=[]
    for i in part4:
        date=datetime.datetime.strptime(i, "%Y-%m-%d")
        new_date = date + datetime.timedelta(days=23)
        aa4.append(new_date.strftime("%Y-%m-%d"))
    return pd.Series(aa1+aa2+aa3+aa4)

#application review series-HR
b4_thanksgiving_split1=hr_date_generator_nonholiday(b4_thanksgiving_split[0].application_recieved,
                                                   b4_thanksgiving_split[1].application_recieved,
                                                   b4_thanksgiving_split[2].application_recieved,
                                                   b4_thanksgiving_split[3].application_recieved)
b4_christmas_split1=hr_date_generator_nonholiday(b4_christmas_split[0].application_recieved,
                                                   b4_christmas_split[1].application_recieved,
                                                   b4_christmas_split[2].application_recieved,
                                                   b4_christmas_split[3].application_recieved)
b4_covid19_split1=hr_date_generator_nonholiday(b4_covid19_split[0].application_recieved,
                                                   b4_covid19_split[1].application_recieved,
                                                   b4_covid19_split[2].application_recieved,
                                                   b4_covid19_split[3].application_recieved)
thanksgiving_holidays_split1=hr_date_generator_holiday(thanksgiving_holidays_split[0].application_recieved,
                                                   thanksgiving_holidays_split[1].application_recieved,
                                                   thanksgiving_holidays_split[2].application_recieved,
                                                   thanksgiving_holidays_split[3].application_recieved)
christmas_holidays_split1=hr_date_generator_holiday(christmas_holidays_split[0].application_recieved,
                                                   christmas_holidays_split[1].application_recieved,
                                                   christmas_holidays_split[2].application_recieved,
                                                   christmas_holidays_split[3].application_recieved)
during_covid19_split1=hr_date_generator_covid19(during_covid19_split[0].application_recieved,
                                                   during_covid19_split[1].application_recieved,
                                                   during_covid19_split[2].application_recieved,
                                                   during_covid19_split[3].application_recieved)

application_review_data=b4_thanksgiving_split1.append(thanksgiving_holidays_split1)
application_review_data=application_review_data.append(b4_christmas_split1)
application_review_data=application_review_data.append(christmas_holidays_split1)
application_review_data=application_review_data.append(b4_covid19_split1)
application_review_data=application_review_data.append(during_covid19_split1)

new_data['application_evaluation']=list(application_review_data)
new_data['application_evaluation'].replace(holiday2_weekends,inplace=True)

def date_groups(df, column_name):
    #rule-6 person staff
    b4_thanksgiving=df[column_name<'2019-11-20'] 
    #rule=3 person staff
    thanksgiving_holidays=df[(column_name>='2019-11-20') & (column_name<'2019-12-01')]
    #rule-6 person staff
    b4_christmas=df[(column_name>='2019-12-01') & (column_name<'2019-12-21')]
    #rule=3 person staff
    christmas_holidays=df[(column_name>='2019-12-21') & (column_name<'2020-01-02')]
    #rule-6 person staff
    b4_covid19=df[(column_name>='2020-01-02') & (column_name<'2020-03-17')]
    #rule-2 person staff
    during_covid19=df[column_name>='2020-03-17']

    return b4_thanksgiving,thanksgiving_holidays,b4_christmas,christmas_holidays,b4_covid19,during_covid19

certificate_data=date_groups(new_data, new_data.application_evaluation)

aa1=[]
for i in certificate_data[0].application_evaluation:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=1)
    aa1.append(new_date.strftime("%Y-%m-%d"))
aa2=[]
for i in certificate_data[1].application_evaluation:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=3)
    aa2.append(new_date.strftime("%Y-%m-%d"))
aa3=[]
for i in certificate_data[2].application_evaluation:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=1)
    aa3.append(new_date.strftime("%Y-%m-%d"))
aa4=[]
for i in certificate_data[3].application_evaluation:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=5)
    aa4.append(new_date.strftime("%Y-%m-%d"))
aa5=[]
for i in certificate_data[4].application_evaluation:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=1)
    aa5.append(new_date.strftime("%Y-%m-%d"))
aa6=[]
for i in certificate_data[5].application_evaluation:
    date=datetime.datetime.strptime(i, "%Y-%m-%d")
    new_date = date + datetime.timedelta(days=12)
    aa6.append(new_date.strftime("%Y-%m-%d"))

certificate=pd.Series(aa1+aa2+aa3+aa4+aa5+aa6)

new_data['certificate']=list(certificate)

new_data['certificate'].replace(holiday2_weekends,inplace=True)

selecting_manager_review=date_groups(new_data, new_data.certificate)

sm_b4_thankgiving=.9*len(selecting_manager_review[0])
sm_thankgiving=.9*len(selecting_manager_review[1])
sm_b4_christmas=.9*len(selecting_manager_review[2])
sm_christmas=.9*len(selecting_manager_review[3])
sm_b4_covid19=.9*len(selecting_manager_review[4])
sm_covid19=.9*len(selecting_manager_review[5])

a=selecting_manager_review[0].certificate
b=selecting_manager_review[1].certificate
c=selecting_manager_review[2].certificate
d=selecting_manager_review[3].certificate
e=selecting_manager_review[4].certificate
f=selecting_manager_review[5].certificate

def grouper(iterable, n, fillvalue=None):
    "Collect data into fixed-length chunks or blocks"
    # grouper('ABCDEFG', 3, 'x') --> ABC DEF Gxx"
    args = [iter(iterable)] * (n)
    return itertools.zip_longest(*args, fillvalue=fillvalue)
gr_a=list(grouper(a,int(len(a)/int(.01+len(selecting_manager_review[0])-(sm_b4_thankgiving))+1)))
gr_b=list(grouper(b,int(len(b)/int(.01+len(selecting_manager_review[1])-(sm_thankgiving))+1)))
gr_c=list(grouper(c,int(len(c)/int(.01+len(selecting_manager_review[2])-(sm_b4_christmas))+1)))
gr_d=list(grouper(d,int(len(d)/int(.01+len(selecting_manager_review[3])-(sm_christmas))+1)))
gr_e=list(grouper(e,int(len(e)/int(.01+len(selecting_manager_review[4])-(sm_b4_covid19))+1)))
gr_f=list(grouper(f,int(len(f)/int(.01+len(selecting_manager_review[5])-(sm_covid19))+1)))

def group_personnel(group, data):
    series=[pd.Series(i).replace(pd.Series(i)[:1],'None') for i in group][:-1]
    group_list=list(itertools.chain.from_iterable(series))
    group_list=group_list+(group_list[-1:]*(len(data)-len(group_list)))
    return group_list

gr_sm_b4_thanksgiving1=group_personnel(gr_a,a)
gr_sm_thankgiving1=group_personnel(gr_b,b)
gr_sm_b4_christmas1=group_personnel(gr_c,c)
gr_sm_christmas1=group_personnel(gr_d,d)
gr_sm_b4_covid191=group_personnel(gr_e,e)
gr_sm_covid191=group_personnel(gr_f,f)

mananger_review_data=pd.Series(gr_sm_b4_thanksgiving1).append(pd.Series(gr_sm_thankgiving1))
mananger_review_data=mananger_review_data.append(pd.Series(gr_sm_b4_christmas1))
mananger_review_data=mananger_review_data.append(pd.Series(gr_sm_christmas1))
mananger_review_data=mananger_review_data.append(pd.Series(gr_sm_b4_covid191))
mananger_review_data=mananger_review_data.append(pd.Series(gr_sm_covid191))

mananger_review_data_l=mananger_review_data.copy()

mananger_review_data=pd.Series(mananger_review_data).reset_index()
mananger_review_data=mananger_review_data.drop('index', axis=1)

new_data['mananger_review_data']=mananger_review_data

new_data['mananger_review_data'].replace(holiday2_weekends,inplace=True)

interview_references=new_data.mananger_review_data


interview_references_a=interview_references[:int(len(a))]
interview_references_b=interview_references[int(len(interview_references_a)):int(len(interview_references_a)+int(len(b)))]
interview_references_c=interview_references[int(len(interview_references_a)+int(len(b))):int(len(interview_references_a)+int(len(b))+len(c))]
interview_references_d=interview_references[int(len(interview_references_a)+int(len(b))+len(c)):int(len(interview_references_a)+int(len(b))+len(c)+len(d))]
interview_references_e=interview_references[int(len(interview_references_a)+int(len(b))+len(c)+len(d)):int(len(interview_references_a)+int(len(b))+len(c)+len(d)+int(len(e)))]
interview_references_f=interview_references[int(len(interview_references_a)+int(len(b))+len(c)+len(d)+int(len(e))):int(len(interview_references_a)+int(len(b))+len(c)+len(d)+int(len(e))+int(len(f)))]

interview_references_df=pd.Series(interview_references_a).append(pd.Series(interview_references_b))
interview_references_df=interview_references_df.append(pd.Series(interview_references_c))
interview_references_df=interview_references_df.append(pd.Series(interview_references_d))
interview_references_df=interview_references_df.append(pd.Series(interview_references_e))
interview_references_df=interview_references_df.append(pd.Series(interview_references_f))

def hr_date_generator_interview(part1,part2,part3, part4, part5, part6):
    
    aa1=[]
    for i in part1:
        if str(i)=='None':
            aa1.append(i)
            
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=11)
            aa1.append(new_date.strftime("%Y-%m-%d"))
    aa2=[]
    for i in part2:
        if str(i)=='None':
            aa2.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=17)
            aa2.append(new_date.strftime("%Y-%m-%d"))
    aa3=[]
    for i in part3:
        if str(i)=='None':
            aa3.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=11)
            aa3.append(new_date.strftime("%Y-%m-%d"))
    aa4=[]
    for i in part4:
        if str(i)=='None':
            aa4.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=17)
            aa4.append(new_date.strftime("%Y-%m-%d"))
    aa5=[]
    for i in part5:
        if str(i)=='None':
            aa5.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=10)
            aa5.append(new_date.strftime("%Y-%m-%d"))
    aa6=[]
    for i in part6:
        if str(i)=='None':
            aa6.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=23)
            aa6.append(new_date.strftime("%Y-%m-%d"))
        
    return aa1, aa2, aa3, aa4, aa5, aa6

int_data=hr_date_generator_interview(interview_references_a,interview_references_b,
                                     interview_references_c,interview_references_d,
                                     interview_references_e,interview_references_f)

gr_int_a=list(grouper(pd.Series(int_data[0]),2))
gr_int_b=list(grouper(pd.Series(int_data[1]),2))
gr_int_c=list(grouper(pd.Series(int_data[2]),2))
gr_int_d=list(grouper(pd.Series(int_data[3]),2))
gr_int_e=list(grouper(pd.Series(int_data[4]),2))
gr_int_f=list(grouper(pd.Series(int_data[5]),2))

    
gr_int_b4_thanksgiving1=group_personnel(gr_int_a, int_data[0])
gr_int_thankgiving1=group_personnel(gr_int_b, int_data[1])
gr_int_b4_christmas1=group_personnel(gr_int_c, int_data[2])
gr_int_christmas1=group_personnel(gr_int_d, int_data[3])
gr_int_b4_covid191=group_personnel(gr_int_e, int_data[4])
gr_int_covid191=group_personnel(gr_int_f, int_data[5])

#selecting official review resumes, conduct interviews, check references -15 days
interview_date=pd.Series(gr_int_b4_thanksgiving1).append(pd.Series(gr_int_thankgiving1))
interview_date=interview_date.append(pd.Series(gr_int_b4_christmas1))
interview_date=interview_date.append(pd.Series(gr_int_christmas1))
interview_date=interview_date.append(pd.Series(gr_int_b4_covid191))
interview_date=interview_date.append(pd.Series(gr_int_covid191))

interview_datep=pd.Series(interview_date).reset_index()

interview_datep=interview_datep.drop('index', axis=1)

new_data['interview_date']=interview_datep

new_data['interview_date'].replace(holiday2_weekends,inplace=True)

#conditional job offer - assume one job offer for 10 positions twice a month

#get seasonal groups
dic={}
for i,k in enumerate(new_data.interview_date):
    dic[k]=i
    
aa=new_data[new_data.interview_date<='2019-11-20'][-1:].interview_date.reset_index()
ab=new_data[new_data.interview_date<='2019-12-01'][-1:].interview_date.reset_index()
ac=new_data[new_data.interview_date<='2019-12-21'][-1:].interview_date.reset_index()
ad=new_data[new_data.interview_date<='2020-01-02'][-1:].interview_date.reset_index()
ae=new_data[new_data.interview_date<='2020-03-17'][-1:].interview_date.reset_index()
af=new_data[new_data.interview_date>='2020-03-16'][-1:].interview_date.reset_index()

offer_b4_thanksgiving_=new_data.interview_date[:int(aa['index'])+1]
offer_thanksgiving_=new_data.interview_date[int(aa['index'])+1:int(ab['index'])+2]
offer_b4_christmas_=new_data.interview_date[int(ab['index'])+2:int(ac['index'])+1]
offer_christmas_=new_data.interview_date[int(ac['index'])+1:int(ad['index'])+1]
offer_b4_covid19_=new_data.interview_date[int(ad['index'])+1:int(ae['index'])+1]
offer_covid19_=new_data.interview_date[int(ae['index'])+1:int(af['index'])+1]

def hr_date_generator_offer(part1,part2,part3, part4, part5, part6):
    
    aa1=[]
    for i in part1:
        if str(i)=='None':
            aa1.append(i)
            
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=2)
            aa1.append(new_date.strftime("%Y-%m-%d"))
    aa2=[]
    for i in part2:
        if str(i)=='None':
            aa2.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=5)
            aa2.append(new_date.strftime("%Y-%m-%d"))
    aa3=[]
    for i in part3:
        if str(i)=='None':
            aa3.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=2)
            aa3.append(new_date.strftime("%Y-%m-%d"))
    aa4=[]
    for i in part4:
        if str(i)=='None':
            aa4.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=6)
            aa4.append(new_date.strftime("%Y-%m-%d"))
    aa5=[]
    for i in part5:
        if str(i)=='None':
            aa5.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=2)
            aa5.append(new_date.strftime("%Y-%m-%d"))
    aa6=[]
    for i in part6:
        if str(i)=='None':
            aa6.append(i)
        else:
            date=datetime.datetime.strptime(i, "%Y-%m-%d")
            new_date = date + datetime.timedelta(days=16)
            aa6.append(new_date.strftime("%Y-%m-%d"))
        
    return aa1, aa2, aa3, aa4, aa5, aa6

offer_all=hr_date_generator_offer(offer_b4_thanksgiving_,offer_thanksgiving_,offer_b4_christmas_, offer_christmas_, offer_b4_covid19_, offer_covid19_)

offer_b4_thanksgiving=offer_all[0]
offer_thanksgiving=offer_all[1]
offer_b4_christmas=offer_all[2]
offer_christmas=offer_all[3]
offer_b4_covid19=offer_all[4]
offer_covid19=offer_all[5]

gr_off_a1=list(grouper(pd.Series(offer_b4_thanksgiving),5))
gr_off_b1=list(grouper(pd.Series(offer_thanksgiving),3))
gr_off_c1=list(grouper(pd.Series(offer_b4_christmas),5))
gr_off_d1=list(grouper(pd.Series(offer_christmas),2))
gr_off_e1=list(grouper(pd.Series(offer_b4_covid19),5))
gr_off_f1=list(grouper(pd.Series(offer_covid19),2))


offer_b4_thanksgiving_samp1=group_personnel(gr_off_a1,offer_b4_thanksgiving)
offer_thanksgiving_samp1=group_personnel(gr_off_b1,offer_thanksgiving)
offer_b4_christmas_samp1=group_personnel(gr_off_c1,offer_b4_christmas)
offer_christmas_samp1=group_personnel(gr_off_d1,offer_christmas)
offer_b4_covid19_samp1=group_personnel(gr_off_e1,offer_b4_covid19)
offer_covid19_samp1=group_personnel(gr_off_f1,offer_covid19)


offer_data=pd.Series(offer_b4_thanksgiving_samp1).append(pd.Series(offer_thanksgiving_samp1))
offer_data=offer_data.append(pd.Series(offer_b4_christmas_samp1))
offer_data=offer_data.append(pd.Series(offer_christmas_samp1))
offer_data=offer_data.append(pd.Series(offer_b4_covid19_samp1))
offer_data=offer_data.append(pd.Series(offer_covid19_samp1))

offer_datap=pd.Series(offer_data).reset_index()

offer_datap=offer_datap.drop('index', axis=1)

new_data['offer_date']=offer_datap

new_data['offer_date'].replace(holiday2_weekends,inplace=True)

new_data.columns=['applicant_id', 'race', 'gender', 'application_date', 'application_recieved',
       'application_evaluation', 'certificate', 'mananger_review_data',
       'interview_date', 'offer_date']

new_data=new_data.iloc[:,2:]

application_date_data=new_data.application_date.value_counts()
application_recieved_data=new_data.application_recieved.value_counts()
application_evaluation_data=new_data.application_evaluation.value_counts()
certificate_data=new_data.certificate.value_counts()
mananger_review_data_data=new_data.mananger_review_data.value_counts()
interview_date_data=new_data.interview_date.value_counts()
offer_date_data=new_data.offer_date.value_counts()

df1=pd.DataFrame(application_date_data).join(pd.DataFrame(application_recieved_data), how='outer')
df2=df1.join(pd.DataFrame(application_evaluation_data), how='outer')
df3=df2.join(pd.DataFrame(certificate_data), how='outer')
df4=df3.join(pd.DataFrame(mananger_review_data_data), how='outer')
df5=df4.join(pd.DataFrame(interview_date_data), how='outer')
df6=df5.join(pd.DataFrame(offer_date_data), how='outer')

df6=df6.reset_index()

df6.columns=['date', 'application_date', 'application_received',
       'application_evaluation', 'certificate', 'manager_review_data',
       'interview_date', 'offer_date']
       
df6=df6.fillna(0)

df6=df6[df6.date!='None']
      
df6.info()
print('\n\nNow saving data to current directory...\n\n')

df6.head()
df6.to_csv('data' +str(randint(1,999999))+'.csv', encoding='utf-8')

