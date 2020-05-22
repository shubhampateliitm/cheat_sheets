## counting lines in a file.
`wc -l file_name`
with pipe ' | wc -l'

## delete last line of file.
with pipe ` | sed '$d'`

## find files. that are recorded after time, and sort them
`find location -newermt time -type f | sort`

## Declare Bad Exit from bash.
` exit 1 `

## Writing function bash
```

function_name(){


}

or

function function_name{

  
}

```
