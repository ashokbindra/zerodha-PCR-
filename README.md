# zerodha-PCR-
hi whats wrong with my coding , it runs successfully but taking too much time to finish the task , it is taking 40 min to 2 hrs , i have laptop with configuration of i7 and 8gb ram
from nsepy import get_history
import pandas as pd
import numpy as np
import kiteconnect
from datetime import datetime, date, timedelta
from kiteconnect import KiteConnect


def write_log(text):
    f = open(r'log.txt', 'a')
    f.write("{}\n".format(text))


class ExcelSheetUpdate(object):

    def __init__(self, **kwargs):

        self.start_date = self.end_date = kwargs.get('date')
        self.csv_file_name = file_name

        # Tickers List
        self.tickers = [
            "ACC","ADANIENT","ADANIPORTS","AMARAJABAT","AMBUJACEM","APOLLOHOSP","APOLLOTYRE","ASHOKLEY","ASIANPAINT","AUROPHARMA",
            "AXISBANK","BAJAJ-AUTO","BAJFINANCE","BAJAJFINSV","BALKRISIND","BANDHANBNK","BANKBARODA","BATAINDIA","BERGEPAINT","BEL",
            "BHARATFORG","BPCL","BHARTIARTL","INFRATEL","BHEL","BIOCON","BOSCHLTD","BRITANNIA","CADILAHC","CANBK","CENTURYTEX",
            "CHOLAFIN","CIPLA","COALINDIA","COLPAL","CONCOR","CUMMINSIND","DABUR","DIVISLAB","DLF","DRREDDY","EICHERMOT","EQUITAS",
            "ESCORTS","EXIDEIND","FEDERALBNK","GAIL","GLENMARK","GMRINFRA","GODREJCP","GODREJPROP","GRASIM","HAVELLS","HCLTECH",
            "HDFCBANK","HDFC","HDFCLIFE","HEROMOTOCO","HINDALCO","HINDPETRO","HINDUNILVR","ICICIBANK","ICICIPRULI","NAUKRI",
            "IDEA","IDFCFIRSTB","IBULHSGFIN","IOC","IGL","INDUSINDBK","INFY","INDIGO","ITC","JINDALSTEL","JSWSTEEL","JUBLFOOD",
            "JUSTDIAL","KOTAKBANK","L&TFH","LT","LICHSGFIN","LUPIN","M&MFIN","MGL","M&M","MANAPPURAM","MARICO","MARUTI","MFSL",
            "MINDTREE","MOTHERSUMI","MRF","MUTHOOTFIN","NATIONALUM","NCC","NESTLEIND","COFORGE","NMDC","NTPC","ONGC","PAGEIND",
            "PETRONET","PIDILITIND","PEL","PFC","POWERGRID","PNB","PVR","RBLBANK","RELIANCE","RECLTD","SHREECEM","SRTRANSFIN",
            "SIEMENS","SRF","SBIN","SBILIFE","SAIL","SUNPHARMA","SUNTV","TATACHEM","TCS","TATACONSUM","TATAMOTORS","TATAPOWER",
            "TATASTEEL","TECHM","RAMCOCEM","TITAN","TORNTPHARM","TORNTPOWER","TVSMOTOR","UJJIVAN","ULTRACEMCO","UBL","MCDOWELL-N",
            "UPL","VEDL","VOLTAS","WIPRO","ZEEL","NIFTY","BANKNIFTY"
        ]

        cash_df = pd.DataFrame() # this variable will contain fetched cash data 

        print('CASH DATA EXTRACTION BEGINS:', datetime.today().time())
        write_log(text=['CASH DATA EXTRACTION BEGINS:', datetime.today().time()])

        # Connecting To Zerodha API (Kite)
        self.kite = KiteConnect(api_key='crpko3b6x4nw47ym')
        self.kite.set_access_token(kite_token)

        # Fetching Derivatives data from API
        derivative_data = self.kite.instruments(exchange='NFO')
        derivative_data = pd.DataFrame(derivative_data)
        derivative_data.expiry = derivative_data.expiry.astype('datetime64[ns]').dt.date
    
        # Slicing options data from derivative data
        self.pc_data = derivative_data[derivative_data['instrument_type'] != 'FUT']
        self.pc_data.reset_index(drop=True, inplace=True)

        pcr = [] # this variable will contain PCR

        print('PCR CALCULATION BEGINS:', datetime.today().time())
        write_log(text=['PCR CALCULATION BEGINS:', datetime.today().time()])

        # this loop will calculate PCR
        for j in self.tickers:

            print(j, datetime.today().time())
            write_log(text=[j, datetime.today().time()])
            

                finally:

                    if chance < 5:
                        if ans > 2.5: # this loop is for replacing PCR value, when we get PCR greater than 2.5
                            ans = 2.5
                        
                        if len(expiry) == 1 and index == 0:
                            ratio = {'Symbol': j, 'PCR': ans, 'Weekly PCR': np.nan}
                            pcr.append(ratio)
                        elif len(expiry) == 2 and index == 0:
                            ratio = {'Symbol': j, 'PCR': ans, 'Weekly PCR': np.nan}
                            pcr.append(ratio)
                        elif len(expiry) == 2 and index == 1:
                            pcr[-1]['Weekly PCR'] = ans
             
        pcr_df = pd.DataFrame(pcr) # this dataframe will now contain PCR of different tickers
        
        print('FUTURE OI CALCULATION BEGINS:', datetime.today().time())
        write_log(text=['FUTURE OI CALCULATION BEGINS:', datetime.today().time()])

        # Slicing futures data from derivative data
        futures_expiry_date = self.get_expiry(self.start_date, symbol='RELIANCE', w_m='MONTHLY')
        future_data = derivative_data[derivative_data['instrument_type'] == 'FUT']
        future_data = future_data[future_data['expiry'] == futures_expiry_date]

        oi_data = [] # this variable will contain OI

      

        print('CREATION OF FINAL DF FOR CSV FILE BEGINS:', datetime.today().time())
        write_log(text=['CREATION OF FINAL DF FOR CSV FILE BEGINS:', datetime.today().time()])

        self.final_data = pd.merge(cash_df, pcr_df, how='outer')
        self.final_data = pd.merge(self.final_data, oi_df, how='outer')
        self.final_data['Date'] = self.start_date.strftime("%d-%m-%Y")
        self.final_data = self.final_data[['Date', 'Symbol', 'Open', 'High', 'Low', 'Close', 'Volume', '%Deliverble', 'OI', 'PCR', 'Weekly PCR']]
        
        # Appending extracted data into exisiting file
        self.final_data.to_csv(self.csv_file_name, mode='a', index=False, header=None)

        print('DATA APPENDED SUCCESSFULLY IN CSV FILE', datetime.today().time())
        write_log(text=['DATA APPENDED SUCCESSFULLY IN CSV FILE', datetime.today().time()])

   
    
    # this function will fetch OHLC, volume, %Deliverable
    def cash_data(self, symbol):
        
        if symbol == 'NIFTY' or symbol == 'BANKNIFTY':
            df = get_history(symbol=symbol, start=self.start_date, end=self.end_date, index=True)
            df = df[['Open', 'High', 'Low', 'Close', 'Volume']]
            df['Symbol'] = symbol
            df['%Deliverble'] = np.nan
        else:
            df = get_history(symbol=symbol, start=self.start_date, end=self.end_date, index=False)
            df = df[['Symbol', 'Open', 'High', 'Low', 'Close', 'Volume', '%Deliverble']]
        
        return df

    # this function will fetch options data
    def options_data(self, symbol, expiry):

        df = self.pc_data[self.pc_data['expiry'] == expiry]
        
        df = df[df['name'] == symbol]
        df.reset_index(drop=True, inplace=True)
        
        call_inst_token = df[df['instrument_type'] == 'CE']['instrument_token'].tolist()
        put_inst_token = df[df['instrument_type'] == 'PE']['instrument_token'].tolist()
        
        total_call_oi = 0
        for ele1 in call_inst_token:
            res1 = self.kite.historical_data(ele1, self.start_date, self.end_date, 'day', continuous=False, oi=True)

            if len(res1) != 0:
                total_call_oi += res1[0]['oi']
        
        total_put_oi = 0
        for ele2 in put_inst_token:
            res2 = self.kite.historical_data(ele2, self.start_date, self.end_date, 'day', continuous=False, oi=True)
            if len(res2) != 0:
                total_put_oi += res2[0]['oi']
        
        try:
            ratio = total_put_oi / total_call_oi
        except ZeroDivisionError:
            ratio = 0
        
        return ratio


# code starts
file_name = r'C:\Users\Dron Bindra\zerodha\data.csv'
kite_token = open("access_token.txt",'r').read()
data = pd.read_csv(file_name)
today_date = datetime.today().date()
last_update_on = pd.to_datetime(data['Date'], format="%d-%m-%Y").dt.date.max()
diff = datetime.today().date() - last_update_on
missing_dates = []

print('CODE STARTS AT:', datetime.today().time())
write_log(text=['CODE STARTS AT:', datetime.today().time()])

for i in range(1, diff.days + 1):
    day = last_update_on + timedelta(days=i)
    
    if day != today_date and get_history('RELIANCE', start=day, end=day).empty is False:

        print('EXTRACTING DATA FOR: %s' %day, datetime.today().time())
        write_log(text=['EXTRACTING DATA FOR: %s' %day, datetime.today().time()])
        ExcelSheetUpdate(date=day)

print('CODE ENDS AT:', datetime.today().time())
write_log(text=['CODE ENDS AT:', datetime.today().time()])
