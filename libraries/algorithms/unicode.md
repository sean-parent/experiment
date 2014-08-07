---
layout: library
title: Unicode
entities:
  - kind: type-function
    name: expand_utf
    declaration: |
        template <typename T, typename U>
        struct exapand_utf : std::integral_constant<std::size_t, /* see table */>;
    parameters:
      - name: T
        description: an 8, 16, or 32 bit integral type for a UTF code unit
      - name: U
        description: an 8, 16, or 32 bit integral type for a UTF code unit
    result:
        name: value
        description: |
            the maximum expansion of code units when converting a UTF sequence from `T` to `U` 
            determined by the following table:
            
            |source/destination|UTF-8|UTF-16|UTF-32|
            |-----------------:|:---:|:----:|:----:|
            |UTF-8             |  1  |   1  |   1  |
            |UTF-16            |  3  |   1  |   1  |
            |UTF-32            |  4  |   2  |   1  |
            
            For example, if the source type is char16_t (representing UTF-16) with length `n` code
            units, then the maximum number of code units needed to represent the same string in
            UTF-8 is `n * expand_utf<char16_t, char>::value`, or `n * 3` `char`s.
    see: [ copy_utf ]
    
  - kind: function
    name: copy_utf
    declaration: |
        template <typename T, typename I, typename O>
        O copy_utf(I f, I l, O o);

        template <typename T, typename R, typename O>
        O copy_utf(const R& r, O o);
    parameters:
      - name: T
        description: an 8, 16, or 32 bit integral type specifying the output UTF code unit
      - name: '[f, l), r'
        description: a valid input range of UTF encoded text
      - name: o
        description: an output iterator, not within the range `[f, l)
    preconditions:
      - |
        There is enough space to hold the text being copied. The maximum requirement on the
        output is that `[o, o + m(l -f))` is a valid range where `m` is
        `expand_utf<value_type(I), T>::value`
    notes:
      - |
        If the source contains an invalid or partial encoding then the output is undefined (debug
        builds may assert). However, the code will not read beyond the specified source range or
        output more than the maximum number of code units.
    return: |
        An output iterator pointing to the end of the encoded text.
---
