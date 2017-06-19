# til Elixir

- Convert a [magic number](https://rachelbythebay.com/w/2016/02/21/malloc/) into ASCII

    ```elixir
    magic_number = 1213486160

    magic_number
    |> Integer.to_string(16)
    |> Stream.unfold(&String.split_at(&1, 2))
    |> Stream.take_while(fn(e) -> e != "" end)
    |> Enum.map(fn(e) -> String.to_integer(e, 16) end)
    # => 'HTTP'
    ```

- Spin off a process but only give it a set amount of time to complete

    ```elixir
    task = Task.async(fn() -> do_a_thing() end)
    case Task.yield(task, timeout) || Task.shutdown(task) do
      {:ok, result} ->
        result
      nil ->
        "timed out and shutdown"
    end
    ```
