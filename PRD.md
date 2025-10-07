# Heuristic Language Model (HLM)

The heuristic language model is a simple system for mimicing the functionality
of an LLM within a domain of questions and answers. For instance there are only
so many ways to ask an LLM to add an entry to your calendar, HLM is an attempt
to capture all of those possibilities explicitly using Regex and provide the
developer a way to call tools based on the users prompts to HLM (much in the
same way that an LLM would call prompts based on user input). 

## Development Notes

 - HLM is a library AND a binary
 - binary: 
   - init config (hlm_gen_config.toml)
   - generate examples long list (hlm_examples.toml)
   - generate correct output for each example 
      - (edit hlm_examples.toml adding answers where were NONE before)
   - generate long-list of templates for each example (hlm_examples.toml)
      - add the template within each example in order to draw the connection
        as to where that template came from.
   - create HLM files (hlms/my-hlm-[hash].hlm) 
      - each hlm file will have success rates and failures baked in
   - list hlm files by success rate
 - based purely on heuristics in rust

Generate 10 example requests of how a variety of different people might request
an entry to be added to their own personal calendar. Use a variety of different
styles to simulate how different people might input the request. Respond ONLY
with a json array. For example: 
[
"sports team general meeting 6pm next friday",
...
]

    - each match could be in the form: 
    - "[phrase] [time] [date]" could match "sports team general meeting 5pm next friday"
    - "add to my [name] calendar: [phase] on [date] at [name]" 
    - "create a new calendar named [single-word]"
 - calculated the similarity of words if no exact matches found
   - https://github.com/life4/textdistance.rs
   - probably use "edit" based algo
   - probably user only the fast algo... I guess the user could specify
     - probably jaro_winkler or levenshtein
 - Bulk generation of what you want your HLM to look like based on actual LLMs
    - use ollama-rs crate ONLY do not use any other LLMs besides ollama!
    - use LLM to generate a long list of example inputs.
    - use LLM to generate the correct output parsing for each input. Save this
      list for manual editing/verification.
      - Populate a list of the CORRECT parsing for each example
         - can be done with an LLM
         - Eg. sports team general meeting 5pm next friday 
             - CORRECT [phrase] = "sports team general meeting"
             - CORRECT [time] = "5pm" 
             - CORRECT [date] = "next friday"
         - Output (TODO correct json) structure the same as async-openai
           output.
            ToolCalls {
              function name: add_to_calendar_relative
              parameters {
                param: "sports team general meeting" "
                time: "5pm"
                date: "next friday"
              }
            }
    - for each example input use LLM to create an input template
      automatically. 
       - add this to the long list of input templates (written in examples)
       - Then we can actually test each of those example heuristics out
         (removing heuristic calls which don't produce the previously
         specified correct output).
    - For each example 
     - verification of heuristics:
      - maintain cache of all LLM evaluations which verify or deny each example
        running through a particular heuristic. This way we can limit LLM
        calls and just retrieve from the cache. 
         - we should persist this cache in a file somewhere
      - Once we have a long list of the heuristics AND a long list of the
        examples we can determine the ordering of the match-phrases
      - Each of the match phrases should be assigned an ID could just be hash
        or a counter. 
      - Start with a specific ordering of the match-phrases (could be random)
         - could retry this process a few times with different random starting
           phrases in order to attempt to find better minima. 
         - For each ordering BEFORE we perform any re-arrangements we should
           attempt to log the %-success for each ordering of the phrases... 
            - DO NOT attempt to find subsequent GOOD matches if a BAD match is
              first found in the list, this information is just for populating
              the %-success.
            - log all failures (which phrases fail) with the success of the
              ordering.
            - ALSO log any of the heuristics which were never used!
      - Go through and attempt to match each example phrase.
         - For each example phrase which matches, perform a verification as to
           if the match seems appropriate. 
           - if GOOD then the order is OKAY
           - if BAD then continue through the list until a match is found
             which matches and is verified to as GOOD. Then move the GOOD
             match to a position in front of the first BAD match. 
           - continue through to the NEXT example (even though this shift will
             have changed all earlier examples).
           - Once all the examples have been gone through run a "%-success"
             analysis and log this information with the current ordering AND
             with the current match proceedure.
           - if not 100% success then go through from the start of the list of
             phrases from the start and attempt to minimize the %failure. 
              - if can't minimize further in say 10 subsequent iterations
                through all the examples then simply accept the best %success
                (or multiple if there are ties) as the best outputs.
          
