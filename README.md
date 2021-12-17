# Jeraldo Letricio Halomoan Manulang
# 12220080

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import json
import streamlit as st

st.title("World's Oil Producting Data")
st.markdown('UAS IF2112 Pemrograman Komputer')
st.markdown('Jeraldo Letricio Halomoan Manulang')
st.markdown('082186627430')

listnamescoun = list()
listcodescoun = list()

fhand = open('kode_negara_lengkap.json')
data = json.load(fhand)              #Membaca file json
for country in data:                 #Loop pada tiap negara di file json
    listtups = [(kategori, hasil) for kategori, hasil in country.items()] #Mengubah dictionaries menjadi list of tuples
    countriesdatas = listtups[0]     #Data nama negara pada tuple pertama
    counpref = countriesdatas[1]     #Nama negara pada tuple indeks pertama
    listnamescoun.append(counpref)
    codeprefdata = listtups[2]       #Data kode negara pada tuple ketiga
    codepref = codeprefdata[1]       #Kode negara pada tuple indeks pertama
    listcodescoun.append(codepref)

dicties = dict(zip(listnamescoun, listcodescoun))   #Memasangkan masing-masing nama dan kode negara pada dictionary

# PROBLEM A
st.markdown("Country's Oil Production Data and Graphic")
selectt=st.selectbox('Select Year: ',selectort)
country_input = input("Input Preffered Country: ")

if country_input in listnamescoun:
    inputcode = dicties[country_input]              #Kode negara yang tepat diassign sesuai dengan nama negara masukan user
else:
    print("Country's Not Found! Enter Valid Country")

readcsv = pd.read_csv('produksi_minyak_mentah.csv')
readcsv.produksi = readcsv.produksi.astype(int)           #Menghilangkan titik pada data produksi
index_csv = readcsv.set_index("kode_negara")              #Mengubah index menjadi kode negara
stproblemdata = index_csv.loc[inputcode]                  #Slicing data hanya sesuai input negara dari user

stproblemdata.plot(x='tahun', y='produksi', kind ='line')
plt.title("Production to Year Graphic")
plt.show()

# PROBLEM B
print("\nCountry's B-Graphic for Preffered Year")
countsize_input = input("Input How Big is Preffered Country: ")
year_input = input("Input Preffered Year (1971-2015): ")

index_csv = readcsv.set_index("tahun")                                #Mengubah index menjadi tahun
sliceyear = index_csv.loc[int(year_input)]                            #Slicing data hanya sesuai input tahun dari user
sorted_prod = sliceyear.sort_values(["produksi"], ascending = False)  #Mengurutkan data produksi dari terbesar ke terkecil
ndproblemdata = sorted_prod.head(int(countsize_input))                #Mengambil data B-besar negara inputan user

ndproblemdata.plot(x="kode_negara", y='produksi', kind ='bar')
plt.title("Country's B-Graphic for Preffered Year")
plt.show()

# PROBLEM C
print("\nCountry's B-Graphic for Accumulated Year")
acc_input = input("Input How Big is Preffered Country : ")

count_code_one = list(sliceyear["kode_negara"])  #Mengambil deretan kode negara dari data di soal sebelumnya
                                                 #Dikarenakan ketiadaan negara Afghanistan pada file csv yang diberikan
index_csv = readcsv.set_index("kode_negara")     #Mengubah index menjadi kode negara
count_code_2 = list()
prodcode_list = list()

for count_x in count_code_one:
    evcountcode = index_csv.loc[count_x]        #Data pada negara tertentu
    prodata = list(evcountcode["produksi"])     #Memasukkan data produksi suatu negara ke dalam list untuk dijumlahkan
    acc_production = sum(prodata)               #Menjumlahkan data produksi untuk mendapat kumulatif produksi
    count_code_2.append(count_x)                #Memasukkan ke dalam list
    prodcode_list.append(acc_production)

sum_rdproblem = {"kode_negara" : count_code_2, "Accumulated Production" : prodcode_list}
rdproblemdata = pd.DataFrame(sum_rdproblem)                                              #Membuat 2 list tadi ke dalam dataframe
rdproblemdata = rdproblemdata.sort_values(["Accumulated Production"], ascending = False) #Mengatur agar urutan dari kumulatif terbesar
dif_rd_problem = rdproblemdata.head(int(acc_input))                                      #Menampilkan hanya data B-besar negara berdasarkan kumulatif

dif_rd_problem.plot(x="kode_negara", y='Accumulated Production', kind ='bar')
plt.title("Country's B-Graphic for Accumulated Year")
plt.show()

#No. 4: Informasi per Tahun
st.markdown('Informasi Berdasarkan Tahun')
selectt2=st.selectbox('Pilih Tahun: ',selectort,key="selectt2")
'''
________________________________________________________________________
'''
#No.4a: Negara Produksi Terbesar di Tahun X
st.markdown('Negara dengan jumlah produksi terbesar')
datano4a=data[data['tahun']==selectt2]
datano4a=datano4a.sort_values(["produksi"],ascending=[0])
datano4a=datano4a[:1]
datano4a[["name","kode_negara","region","sub-region","produksi"]]
'''
________________________________________________________________________
'''
#No.4b: Negara Produksi Terkecil di Tahun X
st.markdown('Negara dengan jumlah produksi terkecil')
datano4b=data[data['tahun']==selectt2]
datano4b=datano4b.sort_values(["produksi"],ascending=[1])
datano4b=datano4b.loc[datano4b["produksi"]>0]
datano4b=datano4b[:1]
datano4b[["name","kode_negara","region","sub-region","produksi"]]
'''
________________________________________________________________________
'''
#No.4c: Negara Produksi Nol di Tahun X
st.markdown('Negara dengan jumlah produksi nol')
datano4c=data[data['tahun']==selectt2]
datano4c=datano4c.sort_values(["name"],ascending=[1])
datano4c=datano4c.loc[datano4c["produksi"]==0]
datano4c[["name","kode_negara","region","sub-region"]]
'''
________________________________________________________________________
'''
st.markdown('Informasi Kumulatif')
'''
'''
#No.4d: Negara Produksi Terbesar Kumulatif
st.markdown('Negara dengan jumlah produksi terbesar kumulatif')
datano4d=data.groupby(["name"])["produksi"].sum().reset_index()
datano4d=datano4d.sort_values(["produksi"],ascending=[0])
datatemp=data
datatemp.drop("produksi", axis=1, inplace=True)
datano4d=pd.merge(datano4d,datatemp,on='name')
datano4d=datano4d.drop_duplicates("name")
datano4d[:1][["name","kode_negara","region","sub-region","produksi"]]
'''
________________________________________________________________________
'''
#No.4e: Negara Produksi Terkecil Kumulatif
st.markdown('Negara dengan jumlah produksi terkecil kumulatif')
datano4e=datano4d.sort_values(["produksi"],ascending=[1])
datano4e=datano4e.loc[datano4e["produksi"]>0]
datano4e=datano4e[:1]
datano4e[["name","kode_negara","region","sub-region","produksi"]]
'''
________________________________________________________________________
'''
#No.4f: Negara Produksi Nol Kumulatif
st.markdown('Negara dengan jumlah produksi nol kumulatif')
datano4f=datano4d.sort_values(["name"],ascending=[1])
datano4f=datano4f.loc[datano4f["produksi"]==0]
datano4f[["name","kode_negara","region","sub-region"]]
