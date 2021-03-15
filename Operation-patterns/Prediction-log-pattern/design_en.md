# Prediction log pattern

## Use case
- To use prediction, latency, and other logs for improving your service.
- If you have concerns about the amount of logs to record on your DWH.
- To monitor and send alert with log.

## Architecture
Logs must be collected to analyze and improve your service. The logs may include prediction results along with input, latency, client events, and related activities. Even though each log may be produced in separate system components, it is reasonable to record them in one DWH. Using a queue to aggregate logs may reduce overload to DWH without losing logs, but you may need to consider if the log processing takes so long that it affects freshness.<br>
Logs have many uses. For instance, if the prediction or user behaviour unexpectedly changes drastically from past, then you may assume there is some anomaly. It is best to send an alert in that case so the anomaly can be analyzed and resolved. This might cause an easy to notice system outage, or it might result in anomalous predictions without any obvious error. To catch either scenario, it is good to have alerts for both the prediction system and the model outputs.


## Diagram
![diagram](diagram.png)


## Pros
- Able to analyze effect of prediction.
- Alert.

## Cons
- Amount of logs may increase cost.

## Needs consideration
- Log frequency and log level.
- Storing frequency and backup.
- Purpose of analysis.
