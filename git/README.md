# TIL git

- Force `git pull` to do nothing

    ```bash
    git() {
      if [[ "$*" =~ "pull" ]]; then
        echo "Don't git pull."
      else
        command git $*
      fi
    }
    ```
