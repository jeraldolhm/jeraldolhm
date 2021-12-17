import pandas as pd
import streamlit as st
import plotly.express as px

#merge data
data=pd.read_csv("produksi_minyak_mentah.csv")
datanegara=pd.read_json('kode_negara_lengkap.json')
datanegara=datanegara.rename(columns={"alpha-3":"kode_negara"})
data=pd.merge(datanegara,data,on='kode_negara')

#selektor
selectorn=data['name'].drop_duplicates()
selectort=data['tahun'].drop_duplicates()
selectorb=[*range(1, 250, 1)]

#judul
st.title('Data Produksi Minyak Dunia')
st.markdown('UAS IF2112 Pemrograman Komputer')
st.markdown('Gerard Gregory 12220095')
'''
________________________________________________________________________
'''
#No.1: Produksi Minyak Tiap Negara Per Tahun
st.markdown('Produksi Minyak Tiap Negara per Tahun')
selectn=st.selectbox('Pilih Negara: ',selectorn)
datano1=data[data['name']==selectn]
datano1graph=px.line(datano1,x="tahun",y="produksi",title=str("Produksi Minyak Negara "+selectn))
st.plotly_chart(datano1graph)
'''
________________________________________________________________________
'''
#No.2: Produksi Minyak n-besar pada Tahun x
st.markdown('Produksi Minyak n-besar Negara per Tahun')
selectt=st.selectbox('Pilih Tahun: ',selectort)
selectbn=st.select_slider('Pilih Banyak Negara: ',options=selectorb, value=5)
datano2=data[data['tahun']==selectt]
datano2=datano2.sort_values(["produksi"],ascending=[0])
datano2=datano2[:selectbn]
datano2graph=px.bar(datano2,x="name",y="produksi",title=str(str(selectbn)+" Negara Terbesar Produksi Minyak pada Tahun "+str(selectt)))
st.plotly_chart(datano2graph)
'''
________________________________________________________________________
'''
#No. 3: Produksi Minyak n-besar Kumulatif
st.markdown('Produksi Minyak n-besar Negara Kumulatif')
selectbn2=st.select_slider('Pilih Banyak Negara: ',options=selectorb, value=5, key="selectbn2")
datano3=data.groupby(["name"])["produksi"].sum().reset_index()
datano3=datano3.sort_values(["produksi"],ascending=[0])
datano3=datano3[:selectbn2]
datano3graph=px.bar(datano3,x="name",y="produksi",title=str(str(selectbn2)+" Negara Terbesar Produksi Minyak Kumulatif"))
st.plotly_chart(datano3graph)
'''
________________________________________________________________________
'''
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
