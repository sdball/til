# TIL Ruby things

## How to benchmark

```ruby
require "benchmark"

Benchmark.bm(max_length_of_report_labels) do |b|
  b.report("label a") { block_to_execute }
  b.report("label b") { another_block_to_execute }
end
```

```ruby
Benchmark.bm(10) do |b|
  b.report("a") { sleep 2 }
  b.report("b") { sleep 3 }
end
                 user     system      total        real
a            0.000000   0.000000   0.000000 (  2.000447)
b            0.000000   0.000000   0.000000 (  3.000804)
```
