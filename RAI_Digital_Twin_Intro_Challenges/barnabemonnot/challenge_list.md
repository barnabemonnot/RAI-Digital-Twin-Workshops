- (conceptual) What are the advantages and disadvantages of breaking down the entire workflow on discrete steps of preparing -> backtesting -> identification -> prediction -> output? Could it be made differently?
- (hands-on) Can you execute the RAI Digital Twin through the CLI? Does the output matches the one that we've seen on the slides?

Pretty much yes. Had to extend the extrapolation period from the default `-e 5` to `-e 100`, so that the extrapolation period was longer. From [this line](https://github.com/reflexer-labs/reflexer-digital-twin/blob/fa7d257c8ef85fb52c0ae50fe7900f4979076eef/rai_digital_twin/__main__.py#L11) I understand that one timestep = 1 hour.

- (hands-on) What happens if you use different CLI arguments?
    - For instance, what happens if extrapolation is done for 2 months rather than 2 weeks?

    My laptop was unhappy with the longer period of simulation, but eventually it was able to spit out the charts. They seem more variable, as we simulate further into the future.

    - Or if the past days to be downloaded are 3 days rather than 14 days?

    Here too there is more variability, as the model is learning on less data points.

- Any other relevant question!

I can't explain why the extrapolations seem to capture _on average_ the fact that RAI is stable (w.r.t USD), but the extrapolated paths mostly seem to have a much higher variance than the historical data. In other words, the ensemble mean over the extrapolated samples looks correct, but individually there doesn't seem to be any path that looks like historical data. This seems less true whenever `ewm=1` and `csi=25%`.