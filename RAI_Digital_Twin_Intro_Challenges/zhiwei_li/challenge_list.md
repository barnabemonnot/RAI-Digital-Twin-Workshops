- (conceptual) What are the advantages and disadvantages of breaking down the entire workflow on discrete steps of preparing -> backtesting -> identification -> prediction -> output? Could it be made differently?

Advantage: modular execution makes it easy to make changes regarding different steps
Done differently? I don't quite understand how the system works if back testing is not returning satisfactory results (will it through an error and goes back to the preparation step)? Also not quite sure how the identification step is utilized.

- (hands-on) Can you execute the RAI Digital Twin through the CLI? Does the output matches the one that we've seen on the slides?
Yes I can execute. Yes the output format looks similar to the slides though the actual numbers are different. Specifically, the back testing part doesn't look as perfect as in the slides (not sure how to exactly replicate those data output...).

- (hands-on) What happens if you use different CLI arguments?
    - For instance, what happens if extrapolation is done for 2 months rather than 2 weeks? 
    The trend of future diverges more under different model parameters.
    - Or if the past days to be downloaded are 3 days rather than 14 days?
    I encountered the error when I downloaded the past 3 days yet asked for extrapolation for 2 days (2x24=48 time steps?)...program returned an error "numpy.linalg.LinAlgError: SVD did not converge in Linear Least Squares". Code being ``python -m rai_digital_twin  -p 3 -e 48``.

- Any other relevant question!
