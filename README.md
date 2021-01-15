# SmartFinancial
Smart Financial FLaNK App

# Queries

select avg(best_bid_prc) as BestBidPrice
from simudynelob;

select symbol, TUMBLE_START(event_time, INTERVAL '1' MINUTE) as tumbleStart, TUMBLE_END(event_time, INTERVAL '1' MINUTE) as tumbleEnd, AVG(CAST(high as DOUBLE)) as avgHigh FROM stockEvents WHERE symbol is not null GROUP BY TUMBLE(event_time, INTERVAL '1' MINUTE), symbol;

Select sym, 
avg(best_ask_prc) as bestAskPrice,
TUMBLE_START(`time`, INTERVAL '1' MINUTE) as tumbleStart, 
TUMBLE_END(`time`, INTERVAL '1' MINUTE) as tumbleEnd 
FROM simudynelob 
GROUP BY TUMBLE(`time`, INTERVAL '1' MINUTE), sym;

use catalog default_catalog;

CREATE TABLE simudynecalcs 
( symbol STRING, uuid STRING, ts BIGINT, dt BIGINT, datetime STRING, open STRING, close STRING, high STRING, volume STRING, low STRING, 
event_time AS CAST(from_unixtime(floor(`time`/1000)) AS TIMESTAMP(3)), 
WATERMARK FOR event_time AS event_time - INTERVAL '5' SECOND )
WITH ( 'connector.type' = 'kafka', 
'connector.version' = 'universal', 
'connector.topic' = 'simudynelob', 
'connector.startup-mode' = 'earliest-offset', 
'connector.properties.bootstrap.servers' = 'edge2ai-1.dim.local:9092', 
'format.type' = 'registry', 
'format.registry.properties.schema.registry.url' = 'http://edge2ai-1.dim.local:7788/api/v1' );

