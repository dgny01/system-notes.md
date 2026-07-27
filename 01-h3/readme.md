Why can't Uber use raw GPS coordinates directly for marketplace analysis?
->Uber groups raw GPS coordinates into H3 cells because analyzing millions of exact locations individually is expensive. Bucketing nearby events into hexagonal cells makes city-wide supply-and-demand analysis more efficient.
  Finding nearby drivers by processing every location individually would consume significant computational resources.
