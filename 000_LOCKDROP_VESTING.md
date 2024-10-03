### Code Deployment for DMZ:

- Transaction Hash: `F1AA84105620F17F241CFAE3380D1DE57EC7107DB7E72AC9743BB0CCA2E3E62A`
- Code ID: `9417`

### Instantiate Message for Lockdrop DMZ:

- Transaction Hash: `<TODO>`
- Code ID: `9417`
- Contract Address: `<TODO>`
- Instantiation Message:

```
{
    "owner": "terra1xnu72mn60yzcyr0fl8avgjy5wepfw85c0knfeh",
    "recipient": "terra1pfefmmls2w67njucd2qgvv4qefcutyl95g986pd69caxdyzp7acsfp0fv8",
    "title": "Juris Lockdrop Round #1",
    "description": "This is the Juris Lockdrop Round #1 that benefits the delegators of the Juris LUNC validator. The eligibility for this lockdrop was determined by taking daily delegation snapshots over a period in August to October of the year 2024",
    "total": "10000000000000000",
    "denom": {
        "cw20": "terra1vhgq25vwuhdhn9xjll0rhl2s67jzw78a4g2t78y5kz89q9lsdskq2pxcj2"
    },
    "schedule": {
        "piecewise_linear": [
            [
                1728086400,
                "0"
            ],
            [
                1743811200,
                "0"
            ],
            [
                1791158400,
                "10000000000000000"
            ]
        ]
    },
    "start_time": 1728086400,
    "vesting_duration_seconds": 63072000,
    "unbonding_duration_seconds": 1814400
}
```
