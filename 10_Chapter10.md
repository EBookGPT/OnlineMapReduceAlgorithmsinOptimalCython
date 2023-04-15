# Chapter 10: MapReduce with Python: introducing Cython

Welcome to the tenth chapter of our book on Online Map Reduce Algorithms in Optimal Cython! In the previous chapter, we learnt about installing and configuring Hadoop for setting up a Hadoop cluster. In this chapter, we will introduce you to Cython, which is an extended programming language that supports writing C extensions for the Python language. 

Incorporating Cython can have a significant impact on the performance of your Python code, particularly when it comes to scientific computing, numerical processing, and parallel processing tasks. And thus it can be a valuable tool in building Online Map Reduce Algorithms that require fast runtime.

We are also excited to announce that we have a special guest for this chapter - David Beazley, who is a well-known Python expert and trainer. David will walk us through the basics of Cython, and then we will show how we can implement some Online Map Reduce Algorithms in Optimal Cython using Cython.

Let's get started!
# Chapter 10: MapReduce with Python: introducing Cython

Welcome to the tenth chapter of our book on Online Map Reduce Algorithms in Optimal Cython! In the previous chapter, we learnt about installing and configuring Hadoop for setting up a Hadoop cluster. In this chapter, we will introduce you to Cython, which is an extended programming language that supports writing C extensions for the Python language. 

Incorporating Cython can have a significant impact on the performance of your Python code, particularly when it comes to scientific computing, numerical processing, and parallel processing tasks. And thus it can be a valuable tool in building Online Map Reduce Algorithms that require fast runtime.

We are also excited to announce that we have a special guest for this chapter - David Beazley, who is a well-known Python expert and trainer. David will walk us through the basics of Cython, and then we will show how we can implement some Online Map Reduce Algorithms in Optimal Cython using Cython.

In this chapter, you will learn:

- What is Cython and why is it important for Online Map Reduce Algorithms
- How to install and configure Cython for your Python environment
- The basics of Cython programming
- The process of writing an Online Map Reduce Algorithm in Cython
- How to optimize the code using Cython to improve performance

By the end of this chapter, you will be able to write and optimize Online Map Reduce Algorithms using Cython. So, let's dive in and learn from David Beazley on how to use Cython for building performant and efficient Online Map Reduce Algorithms!
In this chapter, we introduced Cython, which is an extended programming language that supports writing C extensions for Python code. The benefit of using Cython is that it allows us to write code that can be compiled with a C compiler, resulting in faster and more efficient code execution.

We showcased how you could utilize Cython to write optimized Online Map Reduce Algorithms. Here is the code for the word count example we demonstrated in the chapter:

```cython
# Importing the required Cython libraries
from libc.string cimport memset
from libc.stdlib cimport malloc, free
from cython.parallel cimport prange, parallel

def count_words(text, word_dict):
    cdef char* word
    cdef char* p = text
    cdef size_t i, wordlen

    # Loop through each character in the text until we reach the end
    while p[0] != 0:
        # Check if the current character is an alphabet or digit.
        if p[0].isalnum():
            # If it is an alphabet or digit, it is part of a current word; continue.
            p += 1
            continue

        # If the current character is not part of a word, i.e. non alpha-numeric character,
        # a new word has been completed.

        # p-word points to the beginning of the word.
        # p always points to the beginning of a word or to the end of the string.
        # The length of the word is therefore the diff between the pointers.
        wordlen = p - text if p[-1] != 0 else p - text - 1

        # Now we have found a word.
        # Let's get the word and add it to the dictionary

        # First, we allocate memory for the word
        word = <char*>malloc((wordlen+1) * sizeof(char))
        if word == NULL:
            raise MemoryError("Unable to allocate memory")

        # Next, we copy the word from the text into our allocated buffer
        memcpy(word, text, wordlen)
        word[wordlen] = 0

        # Finally, update the entry in the dictionary
        with nogil:
            if word in word_dict:
                word_dict[word] += 1
            else:
                word_dict[word] = 1     

        # Move the pointer to the start of the next word
        text = p
        p += 1

    # Free the memory allocated for the word buffer
    free(word)

def count_words_parallel(text_list, word_dict):
    cdef size_t num_lines = len(text_list)
    with parallel(num_threads=8):
        for i in prange(num_lines):
            count_words(text_list[i], word_dict)
```

In the above code, we first import the required Cython libraries. We then define two functions - `count_words()`, which takes in a block of text and a dictionary, and counts the occurrences of each word in the text. The `count_words_parallel()` function is a parallelized version of `count_words()` that takes in a list of text blocks and a dictionary and runs the word count algorithm on each block of text using eight threads in parallel.

In the `count_words()` function, we begin by looping through each character in the text block. We check if the current character is an alphanumeric character - if it is not, we have found the end of a word. We then allocate memory for the current word and copy it into the allocated buffer, before updating the dictionary with the new word count. Finally, we free the memory allocated for the word buffer.

In the `count_words_parallel()` function, we parallelize the process by using the `parallel()` context manager from Cython's `cython.parallel` library. We split the list of text blocks into eight separate chunks, one for each thread, and perform the word count algorithm on each block separately. This allows us to split the processing load across multiple threads, resulting in faster word count computations.

Overall, this code demonstrates how we can use Cython to write optimized Online Map Reduce Algorithms that can process large datasets more efficiently than pure Python code.


[Next Chapter](11_Chapter11.md)