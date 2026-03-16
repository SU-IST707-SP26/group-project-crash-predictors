- Does your proposal include all of the above mentioned sections? 1/1
- Are your objectives concrete and do you have a clear stakeholder need? 2/2

Excellent!

- Do you have a good data source and have you done a thorough job investigating its provenance and credibility? 1/1
- Did you do a thorough job exploring your data 2/2
- Have you done some initial modeling of your problem and do you have some early baseline results? 3/3

Really nice work preparing data and modeling.  One thing to note - including post-accident knowledge is a data leakage problem, so you can't really have "factor" in your feature set.  Though an interesting question is whether or not you might predict whether factor is itself predictable, and then use this to predict the likely severity of accidents. 

But a broader question here is what you are doing with intersection data - it seems to me that this is one of your most powerful features, and I'm not seeing it here.  It that because there are too many intersections?  Or is it indeed in there, and I'm just not seeing it? If there's too much data, you might reduce your data to grid cells (e.g. 100 grid cells), or street coordinates, and then use a tree-based methods.  My guess is that will improve things a lot.  You could also consider bringing weather data in, which should also correlated strongly. 

- Do you have a clear path forward 1/1

You guys are doing great work.  If you need more compute power, let me know.

Score: 10/10