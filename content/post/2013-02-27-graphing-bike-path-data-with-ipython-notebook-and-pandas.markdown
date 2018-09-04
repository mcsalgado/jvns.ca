---
categories: []
comments: true
date: 2013-02-27T00:00:00Z
title: Graphing bike path data with IPython Notebook and pandas
url: /blog/2013/02/27/graphing-bike-path-data-with-ipython-notebook-and-pandas/
---

I gave a talk at [Montreal Python](http://montrealpython.org/) on Monday about
graphing [this dataset](http://donnees.ville.montreal.qc.ca/fiche/velos-comptage/) from
Données Ouvertes Montréal. The dataset has the number of bikes seen every day
on the bike paths in Montreal, collected using some sensors 

I showed some graphs of # bikes vs temperature and # bikes vs raininess, using
data downloaded from the Canadian 
[National Climate Archive](http://climate.weatheroffice.gc.ca/climateData/hourlydata_e.html?Prov=QC&StationID=5415&Year=2013&Month=2&Day=16&timeframe=1). 
You can see the IPython notebook here:

[View the notebook using nbviewer](https://nbviewer.jupyter.org/github/jvns/talks/blob/master/2013-04-mtlpy/pistes-cyclables.ipynb)

It's made using [pandas](http://pandas.pydata.org/) and [IPython notebook](http://ipython.org/notebook.html).

If you download it you can run it yourself and do some more complicated
analysis. If you're interested in this, come to the next 
[Python Night](http://montrealpython.org/2013/02/python-nights-3/) on Thursday,
March 7, where we'll be playing with it.
