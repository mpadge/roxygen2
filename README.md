# rxygn

The `rxygn` package is a fork of `roxygen`, and as the name might suggest, it's leaner and meaner.  Compared to `roxygen`, `rxygen`: 

* works with run-time details to give more accurate output - this requires
  that the source code that roxygen is documenting be loaded prior to
  documentation

* is written in idiomatic R

* uses S3 instead of a homegrown class system

* doesn't do call graphs

* roclets build up an internal data structure instead of writing to disk

# Why use rxygn?

The premise of `rxygn` is simple: describe your functions in comments next to where their definitions and `rxygen` will process your source code and comments to produce R compatible Rd files.  Here's a simple example from the `stringr` package:

    #' The length of a string (in characters).
    #'
    #' @param string input character vector
    #' @return numeric vector giving number of characters in each element of the 
    #'   character vector.  Missing string have missing length.
    #' @keywords character
    #' @seealso \code{\link{nchar}} which this function wraps
    #' @export
    #' @examples
    #' str_length(letters)
    #' str_length(c("i", "like", "programming", NA))
    str_length <- function(string) {
      string <- check_string(string)

      nc <- nchar(string, allowNA = TRUE)
      is.na(nc) <- is.na(string)
      nc
    }

When you `roxygenise` your package these comments will be automatically transformed to the Rd file you need to pass `R CMD check`:

    \name{str_length}
    \alias{str_length}
    \title{The length of a string (in characters).}
    \usage{str_length(string)}
    \arguments{
      \item{string}{input character vector}
    }
    \description{
      The length of a string (in characters).
    }
    \seealso{\code{\link{nchar}} which this function wraps}
    \value{numeric vector giving number of characters in each element of the
    character vector.  Missing string have missing length.}
    \keyword{character}
    \examples{
      str_length(letters)
      str_length(c("i", "like", "programming", NA))
    }

# Roclets

`rxygn` comes with three roclets, three tools for parsing your source code and producing files useful for documenting your package:

* `collate_roclet`: allows you to add `@include` directives to ensure that
  files are loaded in the order they are needed

* `namespace_roclet`: creates your `NAMESPACe` automatically. 95% of the time
  all you need to do is label functions, methods and classes that you want to
  export with the `@export` tag

* `rd_roclet`: produces Rd files by inspecting both function definitions and
  roxygen comments in the source code.

By default, `roxygenise` will run all three, but you can choose which ones to run using the `roclet` parameter. It's also possible to write your own roclets - more on this in the future.