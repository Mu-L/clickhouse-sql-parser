# ClickHouse SQL Parser 
![GitHub CI](https://github.com/AfterShip/clickhouse-sql-parser/actions/workflows/ci.yaml/badge.svg) [![Go Report Card](https://goreportcard.com/badge/github.com/AfterShip/clickhouse-sql-parser)](https://goreportcard.com/report/github.com/AfterShip/clickhouse-sql-parser) [![LICENSE](https://img.shields.io/github/license/AfterShip/clickhouse-sql-parser.svg)](https://github.com/AfterShip/clickhouse-sql-parser/blob/master/LICENSE) [![GoDoc](https://img.shields.io/badge/Godoc-reference-blue.svg)](https://godoc.org/github.com/AfterShip/clickhouse-sql-parser) [![Coverage Status](https://coveralls.io/repos/github/AfterShip/clickhouse-sql-parser/badge.svg?branch=master)](https://coveralls.io/github/AfterShip/clickhouse-sql-parser?branch=master) <a href="https://hellogithub.com/repository/23597949cafa410bba6039ddb8867543" target="_blank"><img src="https://api.hellogithub.com/v1/widgets/recommend.svg?rid=23597949cafa410bba6039ddb8867543&claim_uid=kyCYu1VAKgwD8rE&theme=small" alt="Featured｜HelloGitHub" /></a>

The goal of this project is to build a ClickHouse SQL parser in Go with the following key features:

- Parse ClickHouse SQL into AST
- Beautify ClickHouse SQL format

This project is inspired by [memefish](https://github.com/cloudspannerecosystem/memefish) which is a SQL parser for Spanner in Go.
## How to use

You can use it as your Go library or CLI tool, see the following examples:

- Use clickhouse-sql-parser as a Go library

```Go
package main

import (
    clickhouse "github.com/AfterShip/clickhouse-sql-parser/parser"
)

query := "SELECT * FROM clickhouse"
parser := clickhouse.NewParser(query)
// Parse query into AST
statements, err := parser.ParseStmts()
if err != nil {
    return nil, err
}
```

- Install clickhouse-sql-parser as a CLI tool


On Linux:

```bash
$ go install github.com/AfterShip/clickhouse-sql-parser@latest
```

On macOS:

```bash
$ brew install clickhouse-sql-parser
```

Parse ClickHouse SQL into AST or beautify ClickHouse SQL format:

```bash
## Parse query into AST
$ clickhouse-sql-parser "SELECT * FROM clickhouse WHERE a=100"

## Beautify query
$ clickhouse-sql-parser -format "SELECT * FROM clickhouse WHERE a=100"

## Parse query from file
$ clickhouse-sql-parser -file ./test.sql
```

- Parsed tree(AST) back into a SQL statement

```Go
parser := clickhouse.NewParser("SELECT * FROM clickhouse")
// Parse query into AST
statements, err := parser.ParseStmts()
if err != nil {
    return nil, err
}

// Call the String method to unparsed AST into a SQL string
for _, stmt := range statements {
  fmt.Println(stmt.String())
}
```
## Update test assets

For the files inside `output` and `format` dir are generated by the test cases,

if you want to update them, you can run the following command:

```bash
$ make update_test
```

## Benchmarks

```sh
go test -bench=. -benchmem ./parser
```

Results

```
$ go test -bench=. -benchmem ./parser
goos: linux
goarch: amd64
pkg: github.com/AfterShip/clickhouse-sql-parser/parser
cpu: Intel(R) Xeon(R) CPU E5-2697 v3 @ 2.60GHz
BenchmarkParseSQLFiles/access_tuple_with_dot.sql-28                23294             58467 ns/op           13448 B/op        293 allocs/op
BenchmarkParseSQLFiles/query_with_expr_compare.sql-28              43560             25704 ns/op            6240 B/op        132 allocs/op
BenchmarkParseSQLFiles/select_cast.sql-28                          75055             16518 ns/op            4648 B/op         92 allocs/op
BenchmarkParseSQLFiles/select_column_alias_string.sql-28          499798              2785 ns/op             704 B/op         13 allocs/op
BenchmarkParseSQLFiles/select_expr.sql-28                         488187              2448 ns/op             696 B/op         12 allocs/op
BenchmarkParseSQLFiles/select_item_with_modifiers.sql-28           54124             23305 ns/op            6232 B/op        136 allocs/op
BenchmarkParseSQLFiles/select_order_by_timestamp.sql-28           232302              5809 ns/op            1368 B/op         28 allocs/op
BenchmarkParseSQLFiles/select_simple.sql-28                        30602             50022 ns/op            9920 B/op        216 allocs/op
BenchmarkParseSQLFiles/select_simple_field_alias.sql-28           178126              6316 ns/op            1712 B/op         39 allocs/op
BenchmarkParseSQLFiles/select_simple_with_bracket.sql-28           71902             16172 ns/op            3824 B/op         85 allocs/op
BenchmarkParseSQLFiles/select_simple_with_cte_with_column_aliases.sql-28                   67050             19916 ns/op            4648 B/op        101 allocs/op
BenchmarkParseSQLFiles/select_simple_with_group_by_with_cube_totals.sql-28                107047             10348 ns/op            2768 B/op         58 allocs/op
BenchmarkParseSQLFiles/select_simple_with_is_not_null.sql-28                               55285             21957 ns/op            5224 B/op        111 allocs/op
BenchmarkParseSQLFiles/select_simple_with_is_null.sql-28                                   66648             22412 ns/op            4728 B/op        102 allocs/op
BenchmarkParseSQLFiles/select_simple_with_top_clause.sql-28                               269910              4166 ns/op            1088 B/op         22 allocs/op
BenchmarkParseSQLFiles/select_simple_with_with_clause.sql-28                               58494             18417 ns/op            5144 B/op        109 allocs/op
BenchmarkParseSQLFiles/select_table_alias_without_keyword.sql-28                          121261             10011 ns/op            2896 B/op         65 allocs/op
BenchmarkParseSQLFiles/select_table_function_with_query.sql-28                             98017             14929 ns/op            4168 B/op         81 allocs/op
BenchmarkParseSQLFiles/select_when_condition.sql-28                                       220394              5457 ns/op            1304 B/op         28 allocs/op
BenchmarkParseSQLFiles/select_with_distinct.sql-28                                        172948              6531 ns/op            1560 B/op         33 allocs/op
BenchmarkParseSQLFiles/select_with_join_only.sql-28                                       286346              5594 ns/op            1520 B/op         32 allocs/op
BenchmarkParseSQLFiles/select_with_left_join.sql-28                                        88200             13627 ns/op            3880 B/op         75 allocs/op
BenchmarkParseSQLFiles/select_with_literal_table_name.sql-28                              241094              5099 ns/op            1304 B/op         27 allocs/op
BenchmarkParseSQLFiles/select_with_multi_join.sql-28                                       44700             31964 ns/op            8240 B/op        188 allocs/op
BenchmarkParseSQLFiles/select_with_multi_line_comment.sql-28                              363499              4460 ns/op             824 B/op         18 allocs/op
BenchmarkParseSQLFiles/select_with_multi_union.sql-28                                     146233              7827 ns/op            2176 B/op         36 allocs/op
BenchmarkParseSQLFiles/select_with_number_field.sql-28                                    129945              8746 ns/op            2352 B/op         51 allocs/op
BenchmarkParseSQLFiles/select_with_query_parameter.sql-28                                  33850             36346 ns/op            9936 B/op        209 allocs/op
BenchmarkParseSQLFiles/select_with_string_expr.sql-28                                     142882              7530 ns/op            1880 B/op         34 allocs/op
BenchmarkParseSQLFiles/select_with_union_distinct.sql-28                                  147031              9601 ns/op            2352 B/op         47 allocs/op
BenchmarkParseSQLFiles/select_with_variable.sql-28                                        179158              6669 ns/op            1880 B/op         36 allocs/op
BenchmarkParseSQLFiles/select_with_window_function.sql-28                                  54925             31320 ns/op            6720 B/op        136 allocs/op
BenchmarkParseSQLFiles/select_with_placeholder.sql-28                                    196771              5145 ns/op            1272 B/op         26 allocs/op
BenchmarkParseSQLFiles/set_simple.sql-28                                                  172419              7062 ns/op            2480 B/op         49 allocs/op
BenchmarkParseComplexQueries/testdata/query/select_with_multi_join.sql-28                  39056             36897 ns/op            8240 B/op        188 allocs/op
BenchmarkParseComplexQueries/testdata/query/select_with_window_function.sql-28             47629             29916 ns/op            6720 B/op        136 allocs/op
BenchmarkParseComplexQueries/testdata/query/select_simple_with_with_clause.sql-28                  69210             19731 ns/op            5144 B/op        109 allocs/op
BenchmarkParseComplexQueries/testdata/query/select_with_left_join.sql-28                           74576             15338 ns/op            3880 B/op         75 allocs/op
BenchmarkParseComplexQueries/testdata/benchdata/posthog_huge_0.sql-28                                235           6231253 ns/op         1236189 B/op      26696 allocs/op
BenchmarkParseComplexQueries/testdata/benchdata/posthog_huge_1.sql-28                                279           4438280 ns/op         1043374 B/op      22717 allocs/op
PASS
ok      github.com/AfterShip/clickhouse-sql-parser/parser       66.547s
```

## Contact us

Feel free to open an issue or discussion if you have any issues or questions.
