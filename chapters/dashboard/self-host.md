## Self-host a Dashboard

One can self-host a dashboard locally.

1. `git clone https://github.com/jina-ai/dashboard.git && cd dashboard`.
2. Install dependencies using command `yarn`.
3. Run dashboard via the following ways .

### Run in the debug mode

1. `node testServer`
2.  testServer will be running on `http://localhost:5000` by default
3. `yarn start`
4.  dashboard will be served on `http://localhost:3000` by default

### Run in the live mode

1. `yarn build`
2. `node dashboard`
3. dashboard will be served on `http://localhost:3030` by default

