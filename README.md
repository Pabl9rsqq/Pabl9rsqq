//+------------------------------------------------------------------+
//|                                                    PABLO_NASDAQ_MARKETKILLER.mq5|
//|                        Copyright 2024, MetaQuotes Software Corp. |
//|                                       https://www.mql5.com       |
//+------------------------------------------------------------------+
#property copyright "2024, PABLO_NASDA012"
#property link      "https://www.Markethunterrsa.com"
#property version   "1.00"
#property indicator_separate_window
#property indicator_buffers 6
#property indicator_plots   3
#property strict

// Indicator buffers
double BuyBuffer[];
double SellBuffer[];
double StopLossBuffer[];
double TakeProfit1Buffer[];
double TakeProfit2Buffer[];
double TakeProfit3Buffer[];

// Indicator settings
input int MA_Period = 14;
input double StopLossLevel = 50.0;  // Stop loss in pips
input double TakeProfit1Level = 100.0; // Take profit 1 in pips
input double TakeProfit2Level = 150.0; // Take profit 2 in pips
input double TakeProfit3Level = 200.0; // Take profit 3 in pips

//+------------------------------------------------------------------+
//| Custom indicator initialization function                         |
//+------------------------------------------------------------------+
int OnInit()
  {
   SetIndexBuffer(0, BuyBuffer);
   SetIndexBuffer(1, SellBuffer);
   SetIndexBuffer(2, StopLossBuffer);
   SetIndexBuffer(3, TakeProfit1Buffer);
   SetIndexBuffer(4, TakeProfit2Buffer);
   SetIndexBuffer(5, TakeProfit3Buffer);

   IndicatorSetString(INDICATOR_SHORTNAME, "NonRepaintIndicator");

   return(INIT_SUCCEEDED);
  }
//+------------------------------------------------------------------+
//| Custom indicator iteration function                              |
//+------------------------------------------------------------------+
int OnCalculate(const int rates_total,
                const int prev_calculated,
                const datetime &time[],
                const double &open[],
                const double &high[],
                const double &low[],
                const double &close[],
                const long &tick_volume[],
                const long &volume[],
                const int &spread[])
  {
   int limit = rates_total - prev_calculated;
   if(limit > 0) limit++;

   for(int i = 0; i < limit; i++)
     {
      if(i < MA_Period) continue;

      double MA = iMA(NULL, 0, MA_Period, 0, MODE_SMA, PRICE_CLOSE, i);

      // Bearish trend (sell)
      if(close[i] < MA)
        {
         SellBuffer[i] = close[i];
         StopLossBuffer[i] = close[i] + StopLossLevel * Point();
         TakeProfit1Buffer[i] = close[i] - TakeProfit1Level * Point();
         TakeProfit2Buffer[i] = close[i] - TakeProfit2Level * Point();
         TakeProfit3Buffer[i] = close[i] - TakeProfit3Level * Point();
        }
      // Bullish trend (buy)
      else
        {
         BuyBuffer[i] = close[i];
         StopLossBuffer[i] = close[i] - StopLossLevel * Point();
         TakeProfit1Buffer[i] = close[i] + TakeProfit1Level * Point();
         TakeProfit2Buffer[i] = close[i] + TakeProfit2Level * Point();
         TakeProfit3Buffer[i] = close[i] + TakeProfit3Level * Point();
        }
     }

   return(rates_total);
  }
//+------------------------------------------------------------------+
