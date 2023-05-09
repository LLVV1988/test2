import websocket, json, numpy
import config
from binance.client import Client
from binance.enums import *
from pyti import hull_moving_average
import pandas as pd
from numpy import genfromtxt

global eth_price,balance_in_usd,one_percent_of_global_balance
SOCKET = "wss://stream.binance.com:9443/stream?streams=ethusdt@kline_1m/btcusdt@kline_1m/bnbusdt@kline_1m/ethbtc@kline_1m/adaeth@kline_1m/xrpbnb@kline_1m/shibdoge@kline_1m/adausdt@kline_1m/dogeusdt@kline_1m/shibusdt@kline_1m/xrpusdt@kline_1m"

client = Client(config.API_KEY, config.API_SECRET)

TP_pct= 1.03
TP_pct_sell=0.97

BTCbalance1 = client.get_asset_balance(asset='BTC')
btc_balance = float(BTCbalance1['free'])

ADAbalance1 = client.get_asset_balance(asset='ADA')
ADA_balance = float(ADAbalance1['free'])

ETHbalance1 = client.get_asset_balance(asset='ETH')
eth_balance = float(ETHbalance1['free'])

XRPbalance1 = client.get_asset_balance(asset='XRP')
XRP_balance = float(XRPbalance1['free'])

BNBbalance1 = client.get_asset_balance(asset='BNB')
BNB_balance = float(BNBbalance1['free'])

DOGEbalance1 = client.get_asset_balance(asset='DOGE')
DOGE_balance = float(DOGEbalance1['free'])

SHIBbalance1 = client.get_asset_balance(asset='SHIB')
SHIB_balance = float(SHIBbalance1['free'])



client = Client(config.API_KEY, config.API_SECRET)

print(Client)



balance_in_usd=10000.0

adaeth_balance_usd=2000.00
xrpbnb_balance_usd=2000.00
shibdoge_balance_usd=2000.00

btc_price= 17000.00
ada_price=0.25
eth_price= 1200.00
xrp_price=0.3589
shib_price=0.0000008
bnb_price=243.00
doge_price=1500.00

ethbtc_price=1500.00
adaeth_price=3.00
xrpbnb_price=3.00
shibdoge_price=3.00

#ADAETH
ada_balance_in_usd = ADA_balance*ada_price
eth_balance_in_usd = numpy.multiply(eth_balance,eth_price)
adaeth_balance_usd = ada_balance_in_usd+eth_balance_in_usd
pct_of_adaeth_bal=adaeth_balance_usd/300 ###bal of ada+eth

adaeth_market_order_qty_sell_in_ada=pct_of_adaeth_bal/ada_price        #ADAETH
adaeth_market_order_qty_sell_in_eth=pct_of_adaeth_bal/eth_price      #ADAETH

adaeth_market_order_qty_buy_in_eth=pct_of_adaeth_bal/eth_price          #ADAETH
adaeth_market_order_qty_buy_in_ada=pct_of_adaeth_bal/ada_price 

#XRPBNB
xrp_balance_in_usd = XRP_balance*xrp_price
bnb_balance_in_usd = numpy.multiply(BNB_balance,bnb_price)
xrpbnb_balance_usd = bnb_balance_in_usd+xrp_balance_in_usd
pct_of_xrpbnb_bal=xrpbnb_balance_usd/300 ##bal of xrp+bnb

xrpbnb_market_order_qty_sell_in_xrp=pct_of_xrpbnb_bal/xrp_price         #XRPBNB
xrpbnb_market_order_qty_sell_in_bnb=pct_of_xrpbnb_bal/bnb_price        #XRPBNB

xrpbnb_market_order_qty_buy_in_bnb=pct_of_xrpbnb_bal/bnb_price          #XRPBNB
xrpbnb_market_order_qty_buy_in_xrp=pct_of_xrpbnb_bal/xrp_price

#SHIBDOGE
shib_balance_in_usd = SHIB_balance*shib_price
doge_balance_in_usd = numpy.multiply(DOGE_balance,doge_price)
shibdoge_balance_usd = shib_balance_in_usd+doge_balance_in_usd
pct_of_shibdoge_bal=shibdoge_balance_usd/800 ##bal of shib+doge

shibdoge_market_order_qty_sell_in_shib=pct_of_shibdoge_bal/shib_price   #SHIBDOGE
shibdoge_market_order_qty_sell_in_doge=pct_of_shibdoge_bal/doge_price   #SHIBDOGE

shibdoge_market_order_qty_buy_in_doge=pct_of_shibdoge_bal/doge_price    #SHIBDOGE
shibdoge_market_order_qty_buy_in_shib=pct_of_shibdoge_bal/shib_price   #SHIBDOGE



one_percent_of_global_balance=balance_in_usd/300

market_order_qty_sell=one_percent_of_global_balance/eth_price         #ETHBTC
market_order_qty_sell_in_btc=one_percent_of_global_balance/btc_price,   #ETHBTC

market_order_qty_buy=one_percent_of_global_balance/btc_price           #ETHBTC
market_order_qty_buy_in_eth=one_percent_of_global_balance/eth_price    #ETHBTC


###########################XRP-BNB
xrpbnb_total_bought_qty_in_xrp=0.000000000000000000000000000000000000000000000000001  ##i.e 6 eth bought 
xrpbnb_total_bought_qty_in_bnb=0.000000000000000000000000000000000000000000000000001   ##i.e 6 btc worth of eth bought



xrpbnb_total_sold_qty_in_xrp=float(0.000000000000000000000000000001)       ##i.e 6 eth sold
xrpbnb_total_sold_qty_in_bnb=float(0.0000000000000000000000000000000000000001)       ##i.e 6 btc worth of eth sold


xrpbnb_reserved_bnb_to_buy= xrpbnb_total_sold_qty_in_bnb
xrpbnb_reserved_xrp_to_sell= xrpbnb_total_bought_qty_in_xrp

xrpbnb_avl_bnb_to_buy = BNB_balance - xrpbnb_reserved_bnb_to_buy
xrpbnb_avl_xrp_to_sell =XRP_balance - xrpbnb_reserved_xrp_to_sell

xrpbnb_can_buy = xrpbnb_avl_bnb_to_buy>xrpbnb_market_order_qty_buy_in_bnb
xrpbnb_can_sell = xrpbnb_avl_xrp_to_sell> xrpbnb_market_order_qty_sell_in_xrp

############################SHIB-DOGE


###############################


total_bought_qty_in_eth=0.000000000000000000000000000000000000000000000000001  ##i.e 6 eth bought 
total_bought_qty_in_btc=0.000000000000000000000000000000000000000000000000001   ##i.e 6 btc worth of eth bought

total_bought_qty_in_eth2=round(total_bought_qty_in_eth,4)
total_bought_qty_in_btc2=round(total_bought_qty_in_btc,4)

total_sold_qty_in_eth=float(0.000000000000000000000000000001)       ##i.e 6 eth sold
total_sold_qty_in_btc=float(0.0000000000000000000000000000000000000001)       ##i.e 6 btc worth of eth sold

total_sold_qty_in_eth2=round(total_sold_qty_in_eth,4)       ##i.e 6 eth sold
total_sold_qty_in_btc2=round(total_sold_qty_in_btc,4)       ##i.e 6 btc worth of eth sold



#####ADAETH
adaeth_tp_buy_price_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/adaeth_TP_prices.csv', delimiter=','))
adaeth_tp_sell_price_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/adaeth_TP_sell_prices.csv', delimiter=','))


adaeth_bgt_in_eth_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/adaeth_bgt_in_eth.csv', delimiter=','))
adaeth_total_bought_qty_in_eth=numpy.sum(adaeth_bgt_in_eth_csv)

adaeth_bgt_in_ada_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/adaeth_bgt_in_ada.csv', delimiter=','))
adaeth_total_bought_qty_in_ada=numpy.sum(adaeth_bgt_in_ada_csv)

adaeth_sld_in_eth_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/adaeth_sld_in_eth.csv', delimiter=','))
adaeth_total_sold_qty_in_eth=numpy.sum(adaeth_sld_in_eth_csv)

adaeth_sld_in_ada_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/adaeth_sld_in_ada.csv', delimiter=','))
adaeth_total_sold_qty_in_ada=numpy.sum(adaeth_sld_in_ada_csv)

adaeth_reserved_eth_to_buy= adaeth_total_sold_qty_in_eth
adaeth_reserved_ada_to_sell= adaeth_total_bought_qty_in_ada

adaeth_avl_eth_to_buy = eth_balance - adaeth_reserved_eth_to_buy
adaeth_avl_ada_to_sell =ADA_balance - adaeth_reserved_ada_to_sell

adaeth_can_buy = adaeth_avl_eth_to_buy>adaeth_market_order_qty_buy_in_eth
adaeth_can_sell = adaeth_avl_ada_to_sell> adaeth_market_order_qty_sell_in_ada




######XRPBNB
xrpbnb_tp_buy_price_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_prices.csv', delimiter=','))
xrpbnb_tp_sell_price_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_sell_prices.csv', delimiter=','))

xrpbnb_bgt_in_xrp_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_xrp.csv', delimiter=','))
xrpbnb_total_bought_qty_in_xrp=numpy.sum(xrpbnb_bgt_in_xrp_csv)

xrpbnb_bgt_in_bnb_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_bnb.csv', delimiter=','))
xrpbnb_total_bought_qty_in_bnb=numpy.sum(xrpbnb_bgt_in_bnb_csv)

xrpbnb_sld_in_xrp_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_xrp.csv', delimiter=','))
xrpbnb_total_sold_qty_in_xrp=numpy.sum(xrpbnb_sld_in_xrp_csv)

xrpbnb_sld_in_bnb_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_bnb.csv', delimiter=','))
xrpbnb_total_sold_qty_in_bnb=numpy.sum(xrpbnb_sld_in_bnb_csv)

xrpbnb_reserved_bnb_to_buy= xrpbnb_total_sold_qty_in_bnb
xrpbnb_reserved_xrp_to_sell= xrpbnb_total_bought_qty_in_xrp

xrpbnb_avl_bnb_to_buy = BNB_balance - xrpbnb_reserved_bnb_to_buy
xrpbnb_avl_xrp_to_sell =XRP_balance - xrpbnb_reserved_xrp_to_sell

xrpbnb_can_buy = xrpbnb_avl_bnb_to_buy>xrpbnb_market_order_qty_buy_in_bnb
xrpbnb_can_sell = xrpbnb_avl_xrp_to_sell> xrpbnb_market_order_qty_sell_in_xrp



#####SHIBDOGE

shibdoge_tp_buy_price_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_prices.csv', delimiter=','))
shibdoge_tp_sell_price_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_sell_prices.csv', delimiter=','))

shibdoge_bgt_in_shib_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_shib.csv', delimiter=','))
shibdoge_total_bought_qty_in_shib=numpy.sum(shibdoge_bgt_in_shib_csv)

shibdoge_bgt_in_doge_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_doge.csv', delimiter=','))
shibdoge_total_bought_qty_in_doge=numpy.sum(shibdoge_bgt_in_doge_csv)

shibdoge_sld_in_shib_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_shib.csv', delimiter=','))
shibdoge_total_sold_qty_in_shib=numpy.sum(shibdoge_sld_in_shib_csv)

shibdoge_sld_in_doge_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_doge.csv', delimiter=','))
shibdoge_total_sold_qty_in_doge=numpy.sum(shibdoge_sld_in_doge_csv)

shibdoge_reserved_shib_to_buy= shibdoge_total_sold_qty_in_doge
shibdoge_reserved_doge_to_sell= shibdoge_total_bought_qty_in_shib

shibdoge_avl_doge_to_buy = DOGE_balance - shibdoge_reserved_shib_to_buy
shibdoge_avl_shib_to_sell =SHIB_balance - shibdoge_reserved_doge_to_sell

shibdoge_can_buy = shibdoge_avl_doge_to_buy>shibdoge_market_order_qty_buy_in_doge
shibdoge_can_sell = shibdoge_avl_shib_to_sell> shibdoge_market_order_qty_sell_in_shib

##########ETHBTC
reserved_btc_to_buy= total_sold_qty_in_btc2
reserved_eth_to_sell=round(total_bought_qty_in_eth2,4)

avl_btc_to_buy = btc_balance - reserved_btc_to_buy
avl_eth_to_sell =eth_balance - reserved_eth_to_sell 

can_buy = avl_btc_to_buy>market_order_qty_buy 
can_sell = avl_eth_to_sell> market_order_qty_sell

can_tp_buy= total_bought_qty_in_btc>0.001
can_tp_sell=total_sold_qty_in_eth>0.005


eth_balance_in_usd = eth_balance*eth_price
btc_balance_in_usd = numpy.multiply(btc_balance,btc_price)
balance_in_usd = eth_balance_in_usd+btc_balance_in_usd
print("eth balance =",round(eth_balance,4))
print("ethprice =",eth_price)

print("available btc to buy =",str(avl_btc_to_buy))
print("avl eth to sell",avl_eth_to_sell)
print("reserved btc to buy",reserved_btc_to_buy)
    
def on_open(ws):
    global btc_balance, eth_balance, balance_in_usd,eth_balance_in_usd,btc_balance_in_usd,total_sold_qty_in_btc,xrp_balance_in_usd,ada_balance_in_usd,bnb_balance_in_usd
    global shib_balance_in_usd,doge_balance_in_usd,TOTAL_balance_in_usd

    print('opened connection')
    eth_balance_in_usd = eth_balance*eth_price
    btc_balance_in_usd = btc_balance*btc_price
    xrp_balance_in_usd = XRP_balance *xrp_price
    ada_balance_in_usd = ADA_balance*ada_price
    bnb_balance_in_usd = BNB_balance*bnb_price
    shib_balance_in_usd = SHIB_balance *shib_price
    doge_balance_in_usd = DOGE_balance *doge_price
    
    TOTAL_balance_in_usd=eth_balance_in_usd+btc_balance_in_usd+xrp_balance_in_usd+ada_balance_in_usd+bnb_balance_in_usd+shib_balance_in_usd+doge_balance_in_usd
    balance_in_usd=eth_balance_in_usd+btc_balance_in_usd
    print(TOTAL_balance_in_usd)

def on_close(ws):
    print('closed connection')

def on_message(ws, message):
    global shibdoge_prices,doge_price,shib_price,bnb_price,xrp_price,ada_price,adaeth_balance_usd,xrpbnb_balance_usd,shibdoge_balance_usd,ada_qty
    global pct_of_adaeth_bal,adaeth_can_tp_buy,pct_of_shibdoge_bal,pct_of_xrpbnb_bal,adaeth_can_sell,xrpbnb_can_sell,xrpbnb_can_buy,shibdoge_can_sell,shibdoge_can_buy
    global shib_balance_in_usd,doge_balance_in_usd,bnb_balance_in_usd,ada_balance_in_usd,xrp_balance_in_usd,adaeth_can_tp_buy,adaeth_can_tp_buy,adaeth_can_tp_sell,adaeth_can_buy
    global np_shibdoge_prices,shibdoge_total_sold_qty_in_shib,shibdoge_total_sold_qty_in_doge,shibdoge_avl_doge_to_buy,shibdoge_avl_shib_to_sell
    global xrpbnb_total_bought_qty_in_bnb,xrpbnb_total_bought_qty_in_xrp,XRP_balance,BNB_balance,np_xrpbnb_prices,DOGE_balance,SHIB_balance
    global market_order_qty_sell,market_order_qty_sell_in_btc,total_sold_qty_in_eth2,market_order_qty_buy,market_order_qty_buy_in_eth,adaeth_avl_eth_to_buy
    global total_bought_qty_in_eth2,closing_price,eth_price,btc_price,total_bought_qty_in_btc2,btc_balance,one_percent_of_global_balance,ethbtc_price
    global total_sold_qty_in_btc2, can_buy,can_sell,can_tp_sell,can_tp_buy,adaeth_price,np_adaeth_prices,shibdoge_can_tp_sell,xrpbnb_avl_bnb_to_buy
    global balance_in_usd,eth_balance_in_usd,btc_balance_in_usd,reserved_btc_to_buy,reserved_eth_to_sell,avl_btc_to_buy,avl_eth_to_sell,xrpbnb_price
    global total_bought_qty_in_btc,total_bought_qty_in_eth,eth_balance,total_sold_qty_in_eth,total_sold_qty_in_btc,shibdoge_can_tp_buy,adaeth_avl_ada_to_sell
    global adaeth_total_bought_qty_in_eth,adaeth_total_bought_qty_in_ada,adaeth_market_order_qty_buy_in_ada,ADA_balance,xrpbnb_avl_xrp_to_sell
    global adaeth_market_order_qty_sell_in_ada,adaeth_market_order_qty_sell_in_eth,adaeth_total_sold_qty_in_ada,adaeth_total_sold_qty_in_eth
    global xrpbnb_total_sold_qty_in_xrp,xrpbnb_total_sold_qty_in_bnb,shibdoge_price,shibdoge_total_bought_qty_in_shib,shibdoge_total_bought_qty_in_doge
    ticker_message = json.loads(message)
    ##pprint.pprint(ticker_message)
    
    closing_price = float(ticker_message['data']['k']['c'])
    closing_price_ticker=ticker_message['data']['k']['s']
    
    
    
                 
    
    if closing_price_ticker=='XRPBNB':
        xrpbnb_price = closing_price
        xrpbnb_tp_buy_price_csv = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_prices.csv", delimiter=','))
        xrpbnb_tp_sell_price_csv = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_sell_prices.csv", delimiter=','))
        
        xrpbnb_bgt_in_xrp_csv = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_xrp.csv", delimiter=','))
        xrpbnb_total_bought_qty_in_xrp=numpy.sum(xrpbnb_bgt_in_xrp_csv)
        
        xrpbnb_bgt_in_bnb_csv = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_bnb.csv", delimiter=','))
        xrpbnb_total_bought_qty_in_bnb=numpy.sum(xrpbnb_bgt_in_bnb_csv)
        
        xrpbnb_sld_in_xrp_csv = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_xrp.csv", delimiter=','))
        xrpbnb_total_sold_qty_in_xrp=numpy.sum(xrpbnb_sld_in_xrp_csv)
        
        xrpbnb_sld_in_bnb_csv = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_bnb.csv", delimiter=','))
        xrpbnb_total_sold_qty_in_bnb=numpy.sum(xrpbnb_sld_in_bnb_csv)
        
        xrpbnb_reserved_bnb_to_buy= xrpbnb_total_sold_qty_in_bnb
        xrpbnb_reserved_xrp_to_sell= xrpbnb_total_bought_qty_in_xrp

        xrpbnb_avl_bnb_to_buy = BNB_balance - xrpbnb_reserved_bnb_to_buy
        xrpbnb_avl_xrp_to_sell =XRP_balance - xrpbnb_reserved_xrp_to_sell

        
        
        xrp_balance_in_usd = XRP_balance*xrp_price
        bnb_balance_in_usd = numpy.multiply(BNB_balance,bnb_price)
        xrpbnb_balance_usd = bnb_balance_in_usd+xrp_balance_in_usd
        pct_of_xrpbnb_bal=xrpbnb_balance_usd/280 ##bal of xrp+bnb

        xrpbnb_market_order_qty_sell_in_xrp=pct_of_xrpbnb_bal/xrp_price         #XRPBNB
        xrpbnb_market_order_qty_sell_in_bnb=pct_of_xrpbnb_bal/bnb_price        #XRPBNB

        xrpbnb_market_order_qty_buy_in_bnb=pct_of_xrpbnb_bal/bnb_price          #XRPBNB
        xrpbnb_market_order_qty_buy_in_xrp=pct_of_xrpbnb_bal/xrp_price
        
        xrpbnb_can_buy = xrpbnb_avl_bnb_to_buy>xrpbnb_market_order_qty_buy_in_bnb
        xrpbnb_can_sell = xrpbnb_avl_xrp_to_sell> xrpbnb_market_order_qty_sell_in_xrp
        
        
        if ticker_message['data']['k']['x']==False:
                          
            if any(list(numpy.array((xrpbnb_tp_buy_price_csv)<closing_price))) == True:
                
                xrpbnb_can_tp_buy =list(numpy.array((xrpbnb_tp_buy_price_csv)<closing_price))
                index_nr=xrpbnb_can_tp_buy.index(True)
                sell_qty = xrpbnb_bgt_in_bnb_csv[index_nr] ###returns the value
                fmt_sell_qty =("%.6f"%sell_qty)
                
                try:
                        
                    order = client.order_market_sell(symbol='XRPBNB',quoteOrderQty=fmt_sell_qty)
                    print(order)
                        
                except Exception as e:
                    
                    print("an exception occured - {}".format(e))
                    return False  
                
                xrpbnb_tp_buy_price_csv= numpy.delete(xrpbnb_tp_buy_price_csv,index_nr)
                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_prices.csv",xrpbnb_tp_buy_price_csv,fmt="%1.9f", delimiter=",")
                
                xrpbnb_bgt_in_bnb_csv= numpy.delete(xrpbnb_bgt_in_bnb_csv,index_nr)
                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_bnb.csv",xrpbnb_bgt_in_bnb_csv,fmt="%1.9f", delimiter=",")
                    
                xrpbnb_bgt_in_xrp = numpy.delete(xrpbnb_bgt_in_xrp_csv,index_nr)
                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_xrp.csv",xrpbnb_bgt_in_xrp,fmt="%1.9f", delimiter=",")
                
                for i in order['fills']:
                    print(i['commission'])
                    fees=[]
                    fees.append(float((i['commission'])))
                    xrpbnb_bgt_fees= numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_fees.csv", delimiter=','))
                    np_fees= numpy.array(fees)
                    xrpbnb_bgt_fees = numpy.append(xrpbnb_bgt_fees,np_fees)
                    numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_fees.csv",xrpbnb_bgt_fees,fmt="%1.8f", delimiter=",")
                    fees=[]
                    xrpbnb_total_fees_bgt= numpy.sum(xrpbnb_bgt_fees)
                    print("xrpbnb total fees bought(in bnb):",xrpbnb_total_fees_bgt)
                      
            elif any(list(numpy.array((xrpbnb_tp_sell_price_csv)>closing_price))) == True:
                xrpbnb_can_tp_sell=list(numpy.array((xrpbnb_tp_sell_price_csv)>closing_price))
                index_nr=xrpbnb_can_tp_sell.index(True)
                buy_qty = xrpbnb_sld_in_xrp_csv[index_nr] ###returns the value
                
                fmt_buy_qty =("%.0f"%buy_qty)
                try:
        
                    order = client.order_market_buy(symbol='XRPBNB',quantity=fmt_buy_qty)
                    print(order)
                    
                except Exception as e:
                    print("an exception occured - {}".format(e))
                    return False 
                
                xrpbnb_tp_sell_price_csv= numpy.delete(xrpbnb_tp_sell_price_csv,index_nr)
                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_sell_prices.csv",xrpbnb_tp_sell_price_csv,fmt="%1.9f", delimiter=",")
                
                xrpbnb_sld_in_xrp_csv= numpy.delete(xrpbnb_sld_in_xrp_csv,index_nr)
                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_xrp.csv",xrpbnb_sld_in_xrp_csv,fmt="%1.9f", delimiter=",")
                
                xrpbnb_sld_in_bnb_csv = numpy.delete(xrpbnb_sld_in_bnb_csv,index_nr)
                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_bnb.csv",xrpbnb_sld_in_bnb_csv,fmt="%1.9f", delimiter=",")

                XRPbalance1 = client.get_asset_balance(asset='XRP')
                XRP_balance = float(XRPbalance1['free'])

                BNBbalance1 = client.get_asset_balance(asset='BNB')
                BNB_balance = float(BNBbalance1['free'])
                
                for i in order['fills']:
                    print(i['commission'])
                    fees=[]
                    fees.append(float((i['commission'])))
                    xrpbnb_sld_fees= numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_fees.csv", delimiter=','))
                    np_fees= numpy.array(fees)
                    xrpbnb_sld_fees = numpy.append(xrpbnb_sld_fees,np_fees)
                    numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_fees.csv",xrpbnb_sld_fees,fmt="%1.8f", delimiter=",")
                    fees=[]
                    xrpbnb_total_fees_sld= numpy.sum(xrpbnb_sld_fees)
                    print("xrpbnb total fees sold(in bnb):",xrpbnb_total_fees_sld) 
            
            
        elif ticker_message['data']['k']['x']:
            
            xrpbnb_prices = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_prices.csv", delimiter=','))
            xrpbnb_prices= numpy.append(xrpbnb_prices,closing_price)
            xrpbnb_prices=numpy.delete(xrpbnb_prices,[0])
            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_prices.csv",xrpbnb_prices,fmt="%1.9f", delimiter=",")
            
            np_xrpbnb_prices=numpy.array(xrpbnb_prices)
            
            
            
            
            if len(xrpbnb_prices)>11:
                
                xrpbnb_hma_fast = hull_moving_average.hull_moving_average(np_xrpbnb_prices, 5)
                xrpbnb_hma_slow = hull_moving_average.hull_moving_average(np_xrpbnb_prices, 10)
                
                xrpbnb_last_hma_fast=xrpbnb_hma_fast[-2]
                xrpbnb_last_hma_slow=xrpbnb_hma_slow[-2]
                
                if xrpbnb_hma_fast[-1]>=xrpbnb_hma_slow[-1]:
                    
                    if xrpbnb_last_hma_fast<=xrpbnb_last_hma_slow:
                        print("--------long_signal->XRPBNB")
                        
                        if xrpbnb_can_buy and (xrpbnb_avl_bnb_to_buy>0.2):      
                            
                            fmt_buy_qty =("%.6f"%xrpbnb_market_order_qty_buy_in_bnb)

                            try:                    
                                order = client.order_market_buy(symbol='XRPBNB',quoteOrderQty=fmt_buy_qty)
                                print(order)
                                
                            except Exception as e:
                                print("an exception occured - {}".format(e))
                                return False 
                            
                            xrp_bought = float(order["executedQty"])
                            xrp_bought_in_bnb = float(order["cummulativeQuoteQty"])     
                            xrpbnb_bgt_fees= float(order["cummulativeQuoteQty"])
                            
                            xrpbnb_bgt_in_xrp_csv_list = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_xrp.csv", delimiter=','))
                            xrpbnb_bgt_in_xrp_csv_list = numpy.append(xrpbnb_bgt_in_xrp_csv_list,xrp_bought)
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_xrp.csv",xrpbnb_bgt_in_xrp_csv_list,fmt="%1.9f", delimiter=",")
                            
                            
                            xrpbnb_bgt_in_bnb_csv_list = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_bnb.csv", delimiter=','))
                            xrpbnb_bgt_in_bnb_csv_list = numpy.append(xrpbnb_bgt_in_bnb_csv_list,xrp_bought_in_bnb)
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_in_bnb.csv",xrpbnb_bgt_in_bnb_csv_list,fmt="%1.9f", delimiter=",")
                            
                            xrpbnb_tp_buy_price_csv_list = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_prices.csv', delimiter=','))
                            xrpbnb_tp_price=(xrp_bought_in_bnb/xrp_bought)*TP_pct
                            
                            xrpbnb_tp_buy_price_csv_list = numpy.append(xrpbnb_tp_buy_price_csv_list,xrpbnb_tp_price)
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_prices.csv",xrpbnb_tp_buy_price_csv_list,fmt="%1.9f", delimiter=",")
                            
                            XRPbalance1 = client.get_asset_balance(asset='XRP')
                            XRP_balance = float(XRPbalance1['free'])

                            BNBbalance1 = client.get_asset_balance(asset='BNB')
                            BNB_balance = float(BNBbalance1['free'])
                            
                            for i in order['fills']:
                                print(i['commission'])
                                fees=[]
                                fees.append(float((i['commission'])))
                                xrpbnb_bgt_fees= numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_fees.csv", delimiter=','))
                                np_fees= numpy.array(fees)
                                xrpbnb_bgt_fees = numpy.append(xrpbnb_bgt_fees,np_fees)
                                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_bgt_fees.csv",xrpbnb_bgt_fees,fmt="%1.8f", delimiter=",")
                                fees=[]
                                xrpbnb_total_fees_bgt= numpy.sum(xrpbnb_bgt_fees)
                                print("xrpbnb total fees bought(in bnb):",xrpbnb_total_fees_bgt)

                    
                elif xrpbnb_hma_fast[-1]<=xrpbnb_hma_slow[-1]:
                    
                    if xrpbnb_last_hma_fast>=xrpbnb_last_hma_slow:
                        print("--------short_signal->XRPBNB")
                        
                        if xrpbnb_can_sell:
                            fmt_buy_qty =("%.0f"%xrpbnb_market_order_qty_sell_in_xrp)
                                 
                            try:
                                                              
                                order = client.order_market_sell(symbol='XRPBNB',quantity=fmt_buy_qty)                                
                                print(order)
                                
                            except Exception as e:
                                print("an exception occured - {}".format(e))
                                return False 
                            
                            xrp_sold = float(order["executedQty"])
                            xrpbnb_sold_in_bnb = float(order["cummulativeQuoteQty"])
                            
                            xrpbnb_sld_in_bnb_csv_list = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_bnb.csv', delimiter=','))
                            xrpbnb_sld_in_bnb_csv_list = numpy.append(xrpbnb_sld_in_bnb_csv_list,xrpbnb_sold_in_bnb)
                            numpy.savetxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_bnb.csv',xrpbnb_sld_in_bnb_csv_list,fmt="%1.9f", delimiter=",")
                            
                            
                            xrpbnb_sld_in_xrp_csv_list = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_xrp.csv", delimiter=','))
                            xrpbnb_sld_in_xrp_csv_list = numpy.append(xrpbnb_sld_in_xrp_csv_list,xrp_sold)
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_in_xrp.csv",xrpbnb_sld_in_xrp_csv_list,fmt="%1.9f", delimiter=",")
                            
                            xrpbnb_tp_sell_price_csv_list = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_sell_prices.csv', delimiter=','))
                            xrpbnb_tp_price_sell=(xrpbnb_sold_in_bnb/xrp_sold)*TP_pct_sell
                            xrpbnb_tp_sell_price_csv_list = numpy.append(xrpbnb_tp_sell_price_csv_list,xrpbnb_tp_price_sell)
                            numpy.savetxt('/Users/lukasvanvoorden/FlaskTrading/xrpbnb_TP_sell_prices.csv',xrpbnb_tp_sell_price_csv_list,fmt="%1.9f", delimiter=",")
                            
                                                                            
                            XRPbalance1 = client.get_asset_balance(asset='XRP')
                            XRP_balance = float(XRPbalance1['free'])

                            BNBbalance1 = client.get_asset_balance(asset='BNB')
                            BNB_balance = float(BNBbalance1['free'])
                            
                            for i in order['fills']:
                                print(i['commission'])
                                fees=[]
                                fees.append(float((i['commission'])))                                   
                                xrpbnb_sld_fees= numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_fees.csv", delimiter=','))
                                np_fees= numpy.array(fees)
                                xrpbnb_sld_fees = numpy.append(xrpbnb_sld_fees,np_fees)
                                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/xrpbnb_sld_fees.csv",xrpbnb_sld_fees,fmt="%1.8f", delimiter=",")
                                fees=[]
                                xrpbnb_total_fees_sld= numpy.sum(xrpbnb_sld_fees)
                                print("xrpbnb total fees sold(in bnb):",xrpbnb_total_fees_sld)
                                
                                    
                               
    elif closing_price_ticker=='SHIBDOGE':
        
        DOGEbalance1 = client.get_asset_balance(asset='DOGE')
        DOGE_balance = float(DOGEbalance1['free'])

        SHIBbalance1 = client.get_asset_balance(asset='SHIB')
        SHIB_balance = float(SHIBbalance1['free'])
        
        
        shibdoge_price = closing_price
        shibdoge_tp_buy_price_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_prices.csv', delimiter=','))
        shibdoge_tp_sell_price_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_sell_prices.csv', delimiter=','))
        
        shibdoge_bgt_in_shib_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_shib.csv', delimiter=','))
        shibdoge_total_bought_qty_in_shib=numpy.sum(shibdoge_bgt_in_shib_csv)
        
        shibdoge_bgt_in_doge_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_doge.csv', delimiter=','))
        shibdoge_total_bought_qty_in_doge=numpy.sum(shibdoge_bgt_in_doge_csv)
        
        shibdoge_sld_in_shib_csv = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_shib.csv", delimiter=','))
        shibdoge_total_sold_qty_in_shib=numpy.sum(shibdoge_sld_in_shib_csv)
        
        shibdoge_sld_in_doge_csv = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_doge.csv', delimiter=','))
        shibdoge_total_sold_qty_in_doge=numpy.sum(shibdoge_sld_in_doge_csv)
        
        shibdoge_reserved_shib_to_buy= shibdoge_total_sold_qty_in_doge
        shibdoge_reserved_doge_to_sell= shibdoge_total_bought_qty_in_shib

        shibdoge_avl_doge_to_buy = DOGE_balance - shibdoge_reserved_shib_to_buy
        shibdoge_avl_shib_to_sell =SHIB_balance - shibdoge_reserved_doge_to_sell

        
        shib_balance_in_usd = SHIB_balance*shib_price
        doge_balance_in_usd = numpy.multiply(DOGE_balance,doge_price)
        shibdoge_balance_usd = shib_balance_in_usd+doge_balance_in_usd
        pct_of_shibdoge_bal=shibdoge_balance_usd/1200 ##bal of shib+doge

        shibdoge_market_order_qty_sell_in_shib=pct_of_shibdoge_bal/shib_price   #SHIBDOGE
        shibdoge_market_order_qty_sell_in_doge=pct_of_shibdoge_bal/doge_price   #SHIBDOGE

        shibdoge_market_order_qty_buy_in_doge=pct_of_shibdoge_bal/doge_price    #SHIBDOGE
        shibdoge_market_order_qty_buy_in_shib=pct_of_shibdoge_bal/shib_price   #SHIBDOGE
        
        shibdoge_can_buy = shibdoge_avl_doge_to_buy>shibdoge_market_order_qty_buy_in_doge
        shibdoge_can_sell = shibdoge_avl_shib_to_sell> shibdoge_market_order_qty_sell_in_shib
        
        if ticker_message['data']['k']['x']==False:
            
            
            
            
            if any(list(numpy.array((shibdoge_tp_buy_price_csv)<closing_price))) == True:
                
                for i in shibdoge_tp_buy_price_csv:
                    
                    shibdoge_can_tp_buy =list(numpy.array((shibdoge_tp_buy_price_csv)<closing_price))
                    index_nr=shibdoge_can_tp_buy.index(True)
                    sell_qty = shibdoge_bgt_in_doge_csv[index_nr] ###returns the value
                    fmt_sell_qty =("%.0f"%sell_qty)
                        
                    try:         
                        order = client.order_market_sell(symbol='SHIBDOGE',quoteOrderQty=fmt_sell_qty)
                        print(order)
                            
                    except Exception as e:
                        print("an exception occured - {}".format(e))
                        return False
                    
                    shibdoge_tp_buy_price_csv= numpy.delete(shibdoge_tp_buy_price_csv,index_nr)
                    numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_prices.csv",shibdoge_tp_buy_price_csv,fmt="%1.9f", delimiter=",")
                    
                    shibdoge_bgt_in_doge_csv= numpy.delete(shibdoge_bgt_in_doge_csv,index_nr)
                    numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_doge.csv",shibdoge_bgt_in_doge_csv,fmt="%1.9f", delimiter=",")
                        
                    shibdoge_bgt_in_shib_csv = numpy.delete(shibdoge_bgt_in_shib_csv,index_nr)
                    numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_shib.csv",shibdoge_bgt_in_shib_csv,fmt="%1.9f", delimiter=",")
                
                    
                    for i in order['fills']:
                        print(i['commission'])
                        fees=[]
                        fees.append(float((i['commission'])))
                        shibdoge_bgt_fees= numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_fees.csv", delimiter=','))
                        np_fees= numpy.array(fees)
                        xrpbnb_bgt_fees = numpy.append(shibdoge_bgt_fees,np_fees)
                        numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_fees.csv",shibdoge_bgt_fees,fmt="%1.8f", delimiter=",")
                        fees=[]
                        shibdoge_total_fees_bgt= numpy.sum(shibdoge_bgt_fees)
                        print("shibdoge total fees bought(in bnb):",shibdoge_total_fees_bgt)
                        return True    
                            
                             
                

            
            elif any(list(numpy.array((shibdoge_tp_sell_price_csv)>closing_price))) == True:
                
                
                for i in shibdoge_tp_sell_price_csv:
                    
                    shibdoge_can_tp_sell=list(numpy.array((shibdoge_tp_sell_price_csv)>closing_price))
                    index_nr=shibdoge_can_tp_sell.index(True)
                    buy_qty = shibdoge_sld_in_shib_csv[index_nr] ###returns the value
                    fmt_sell_qty =("%.0f"%buy_qty)

                    try:
                        order = client.order_market_buy(symbol='SHIBDOGE',quantity=fmt_sell_qty)
                        print(order)
                    
                    except Exception as e:
                        print("an exception occured - {}".format(e))
                        return False 
                    
                    shibdoge_tp_sell_price_csv= numpy.delete(shibdoge_tp_sell_price_csv,index_nr)
                    numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_sell_prices.csv",shibdoge_tp_sell_price_csv,fmt="%1.9f", delimiter=",")
                    
                    shibdoge_sld_in_shib_csv= numpy.delete(shibdoge_sld_in_shib_csv,index_nr)
                    numpy.savetxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_shib.csv',shibdoge_sld_in_shib_csv,fmt="%1.9f", delimiter=",")
                    
                    shibdoge_sld_in_doge_csv = numpy.delete(shibdoge_sld_in_doge_csv,index_nr)
                    numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_doge.csv",shibdoge_sld_in_doge_csv,fmt="%1.9f", delimiter=",")

                    
                    
                    
                    for i in order['fills']:
                        print(i['commission'])
                        fees=[]
                        fees.append(float((i['commission'])))
                        shibdoge_sld_fees= numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_fees.csv", delimiter=','))
                        np_fees= numpy.array(fees)
                        shibdoge_sld_fees = numpy.append(shibdoge_sld_fees,np_fees)
                        numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_fees.csv",shibdoge_sld_fees,fmt="%1.8f", delimiter=",")
                        fees=[]
                        shibdoge_total_fees_sld= numpy.sum(shibdoge_sld_fees)
                        print("shibdoge total fees sold(in bnb):",shibdoge_total_fees_sld)
                        return True
                                
        elif ticker_message['data']['k']['x']:
            
            shibdoge_price=closing_price
            shibdoge_prices = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_prices.csv", delimiter=','))           
            shibdoge_prices= numpy.append(shibdoge_prices,closing_price)
            shibdoge_prices=numpy.delete(shibdoge_prices,[0])
            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_prices.csv",shibdoge_prices,fmt="%1.9f", delimiter=",")
            
            np_shibdoge_prices=numpy.array(shibdoge_prices)
                       
            if len(shibdoge_prices)>11:
                
                shibdoge_hma_fast = hull_moving_average.hull_moving_average(np_shibdoge_prices, 5)
                shibdoge_hma_slow = hull_moving_average.hull_moving_average(np_shibdoge_prices, 10)
                
                shibdoge_last_hma_fast=shibdoge_hma_fast[-2]
                shibdoge_last_hma_slow=shibdoge_hma_slow[-2]
                
                if shibdoge_hma_fast[-1]>=shibdoge_hma_slow[-1]:
                    
                    if shibdoge_last_hma_fast<=shibdoge_last_hma_slow:
                        print("--------long_signal->SHIBDOGE")
                        
                        if shibdoge_can_buy:   
                            fmt_sell_qty =("%.0f"%shibdoge_market_order_qty_buy_in_doge)

                            try:
                                
                                order = client.order_market_buy(symbol='SHIBDOGE',quoteOrderQty=fmt_sell_qty)
                                print(order)
                         
                            except Exception as e:
                                print("an exception occured - {}".format(e))
                                return False 
                        
                            shib_bought = float(order["executedQty"])
                            shib_bought_in_doge = float(order["cummulativeQuoteQty"])   

                            
                            shibdoge_bgt_in_shib_csv_list = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_shib.csv', delimiter=','))
                            shibdoge_bgt_in_shib_csv_list = numpy.append(shibdoge_bgt_in_shib_csv_list,shib_bought)
                            
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_shib.csv",shibdoge_bgt_in_shib_csv_list,fmt="%1.9f", delimiter=",")
                            
                            
                            shibdoge_bgt_in_doge_csv_list = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_doge.csv", delimiter=','))
                            shibdoge_bgt_in_doge_csv_list = numpy.append(shibdoge_bgt_in_doge_csv_list,shib_bought_in_doge)
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_in_doge.csv",shibdoge_bgt_in_doge_csv_list,fmt="%1.9f", delimiter=",")
                            
                            shibdoge_tp_buy_price_csv_list = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_prices.csv', delimiter=','))
                            shibdoge_tp_price=(shib_bought_in_doge/shib_bought)*TP_pct
                            shibdoge_tp_buy_price_csv_list = numpy.append(shibdoge_tp_buy_price_csv_list,shibdoge_tp_price)
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_prices.csv",shibdoge_tp_buy_price_csv_list,fmt="%1.9f", delimiter=",")    
                        
                            DOGEbalance1 = client.get_asset_balance(asset='DOGE')
                            DOGE_balance = float(DOGEbalance1['free'])

                            SHIBbalance1 = client.get_asset_balance(asset='SHIB')
                            SHIB_balance = float(SHIBbalance1['free'])
                            
                            for i in order['fills']:
                                print(i['commission'])
                                fees=[]
                                fees.append(float((i['commission']))) 
                                shibdoge_bgt_fees= numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_fees.csv", delimiter=','))
                                np_fees= numpy.array(fees)
                                shibdoge_bgt_fees = numpy.append(shibdoge_bgt_fees,np_fees)
                                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_bgt_fees.csv",shibdoge_bgt_fees,fmt="%1.8f", delimiter=",")
                                fees=[]   
                                shibdoge_total_fees_bgt= numpy.sum(shibdoge_bgt_fees)
                                print("shibdoge total fees bought(in bnb):",shibdoge_total_fees_bgt)
                                
                    
                elif shibdoge_hma_fast[-1]<=shibdoge_hma_slow[-1]:
                    
                    if shibdoge_last_hma_fast>=shibdoge_last_hma_slow:
                        print("--------short_signal->SHIBDOGE")
                        
                        if shibdoge_can_sell:
                            fmt_sell_qty =("%.0f"%shibdoge_market_order_qty_sell_in_shib)
                                  
                            try:
                                                             
                                order = client.order_market_sell(symbol='SHIBDOGE',quantity=fmt_sell_qty)
                                print(order)
                            except Exception as e:
                                print("an exception occured - {}".format(e))
                                return False 
                            
                            shib_sold = float(order["executedQty"])
                            shib_sold_in_doge = float(order["cummulativeQuoteQty"])
                            
                            
                            shibdoge_sld_in_doge_csv_list = numpy.array(genfromtxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_doge.csv', delimiter=','))
                            shibdoge_sld_in_doge_csv_list = numpy.append(shibdoge_sld_in_doge_csv_list,shib_sold_in_doge)
                            numpy.savetxt('/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_doge.csv',shibdoge_sld_in_doge_csv_list,fmt="%1.9f", delimiter=",")
                            
                            
                            shibdoge_sld_in_shib_csv_list = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_shib.csv", delimiter=','))
                            shibdoge_sld_in_shib_csv_list = numpy.append(shibdoge_sld_in_shib_csv_list,shib_sold)
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_in_shib.csv",shibdoge_sld_in_shib_csv_list,fmt="%1.9f", delimiter=",")
                            
                            shibdoge_tp_sell_price_csv_list = numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_sell_prices.csv", delimiter=','))
                            shibdoge_tp_price_sell=(shib_sold_in_doge/shib_sold)*TP_pct_sell
                            shibdoge_tp_sell_price_csv_list = numpy.append(shibdoge_tp_sell_price_csv_list,shibdoge_tp_price_sell)
                            numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_TP_sell_prices.csv",shibdoge_tp_sell_price_csv_list,fmt="%1.9f", delimiter=",") 
                            
                                                    
                            DOGEbalance1 = client.get_asset_balance(asset='DOGE')
                            DOGE_balance = float(DOGEbalance1['free'])

                            SHIBbalance1 = client.get_asset_balance(asset='SHIB')
                            SHIB_balance = float(SHIBbalance1['free'])
                            
                            for i in order['fills']:
                                print(i['commission'])
                                fees=[]
                                fees.append(float((i['commission'])))
                                shibdoge_sld_fees= numpy.array(genfromtxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_fees.csv", delimiter=','))
                                np_fees= numpy.array(fees)
                                shibdoge_sld_fees = numpy.append(shibdoge_sld_fees,np_fees)
                                numpy.savetxt("/Users/lukasvanvoorden/FlaskTrading/shibdoge_sld_fees.csv",shibdoge_sld_fees,fmt="%1.8f", delimiter=",")
                                fees=[]
                                shibdoge_total_fees_sld= numpy.sum(shibdoge_sld_fees)
                                print("shibdoge total fees sold(in bnb):",shibdoge_total_fees_sld)
                                
                                          
    
    elif closing_price_ticker=='ETHUSDT':
           
        eth_price = float(ticker_message['data']['k']['c'])
    
    elif closing_price_ticker=='DOGEUSDT':
        
        doge_price = float(ticker_message['data']['k']['c'])
    
    elif closing_price_ticker=='SHIBUSDT':
        
        shib_price = float(ticker_message['data']['k']['c'])
     
    elif closing_price_ticker=='XRPUSDT':
        
        xrp_price = float(ticker_message['data']['k']['c'])
     
    elif closing_price_ticker=='BNBUSDT':
        
        bnb_price = float(ticker_message['data']['k']['c']) 
        
    elif closing_price_ticker=='BTCUSDT':
        
        btc_price = float(ticker_message['data']['k']['c'])
    
    elif closing_price_ticker=='ADAUSDT':
        
        ada_price = float(ticker_message['data']['k']['c'])
                
    else:

        print("---$$$---")
        
        btc_balance_in_usd = numpy.multiply(btc_balance,btc_price)
        eth_balance_in_usd = numpy.multiply(eth_balance,eth_price)
        shib_balance_in_usd = numpy.multiply(SHIB_balance,shib_price)
        ada_balance_in_usd = numpy.multiply(ADA_balance,ada_price)
        xrp_balance_in_usd = numpy.multiply(XRP_balance,xrp_price)
        bnb_balance_in_usd = numpy.multiply(BNB_balance,bnb_price)
        doge_balance_in_usd = numpy.multiply(DOGE_balance,doge_price)
        
        adaeth_balance_usd = ada_balance_in_usd+eth_balance_in_usd
        xrpbnb_balance_usd = bnb_balance_in_usd+xrp_balance_in_usd
        shibdoge_balance_usd = shib_balance_in_usd+doge_balance_in_usd
        
        pct_of_adaeth_bal=adaeth_balance_usd/300 ###bal of ada+eth
        pct_of_xrpbnb_bal=xrpbnb_balance_usd/300 ##bal of xrp+bnb
        pct_of_shibdoge_bal=shibdoge_balance_usd/800 ##bal of shib+doge

        
        balance_in_usd = eth_balance_in_usd+btc_balance_in_usd
        one_percent_of_global_balance=balance_in_usd/90
        
        reserved_btc_to_buy= total_sold_qty_in_btc

        eth_balance_in_usd = eth_balance*eth_price
        btc_balance_in_usd = numpy.multiply(btc_balance,btc_price)
        balance_in_usd = eth_balance_in_usd+btc_balance_in_usd

                
                                        
ws = websocket.WebSocketApp(SOCKET, on_open=on_open, on_close=on_close, on_message=on_message)
ws.run_forever()