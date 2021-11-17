### Challenges Overview 

- (conceptual) What are the advantages and disadvantages of breaking down the entire workflow on discrete steps of preparing -> backtesting -> identification -> prediction -> output? Could it be made differently?
- (hands-on) Can you execute the RAI Digital Twin through the CLI? Does the output matches the one that we've seen on the slides?
- (hands-on) What happens if you use different CLI arguments?

#### What are the advantages and disadvantages of breaking down the entire workflow on discrete steps of preparing -> backtesting -> identification -> prediction -> output? Could it be made differently?

If it's something like a parser or software something to clean the data you can potentially run it once and repeat. If it's expensive you can clean the raw data and share it.

With regards to backtesting I think it will lead to a cleaner system and repeatable system if you are splitting that away from your core logic. It means you can capture the parameters, seeds, etc... that you're trying to fit, with the agents behaviors and repro your analysis steps more easily. Additionally, it's probably easier to test your agents in isolation than as apart of the other steps.

Identification ditto^^ same with output/prediction

I think a disadvantage sometimes in these system is parameter capture, and the ability to communicate between the components. I think tools around specification and descriptions like JSON-schema can help in this regard. Think saving and restoration.


--- 
I think in terms of difference. Some systems don't have data to back test against, so you might have to make assumptions about behaviors, without back testing. That might be a more Design Digital Twin vs. Operational.  I think also maybe identification could be done before backtesting. By that I assume figuring out the distributions of the historical data/ that kind of identification, might even be useful for doing doing the initial agent behaviors or white box mechanism design ( spitballin' :D ).


#### (hands-on) Can you execute the RAI Digital Twin through the CLI? Does the output matches the one that we've seen on the slides? 
Yes, I can execute through the cli, this first run from cli does not quite match, the date ranges are not quite aligned. [example](2021-11-11 06:35:34.433304-extrapolation.html)


#### (hands-on) What happens if you use different CLI arguments? 
You get different results, and the state of the various steps gets saved in data, 
so if you want to re run or know what happened you're able to work those things out I think in independent phases.



