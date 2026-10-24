# Combined References

This folder groups the documents from the repository root into one standalone reference per category.
The original files in the root are unchanged; these are additional, consolidated copies.

There are two kinds of combined file:

- **Merged** — the sources overlapped heavily, so they were rewritten into one document organised by topic.
  Each topic appears once, with every unique explanation and code example from the sources kept.
- **Sectioned** — the sources cover different subjects, so each one appears in full as its own part,
  with a contents list at the top linking to each part.

## Files

| File | Topic | Format | Source files |
|---|---|---|---|
| [cpp_core_and_advanced.md](cpp_core_and_advanced.md) | C++ basics, OOP and polymorphism, object lifetime, value categories, callables | Merged | [cpp_complete_reference_basic_core_advanced.md](../cpp_complete_reference_basic_core_advanced.md), [cpp_advanced_concepts_complete_reference.md](../cpp_advanced_concepts_complete_reference.md), [implicit_object_lifecycle_cpp.md](../implicit_object_lifecycle_cpp.md), [cpp_value_categories_complete_explanation.md](../cpp_value_categories_complete_explanation.md) |
| [modern_cpp_11_14.md](modern_cpp_11_14.md) | C++11 / C++14 language features and utilities | Merged | [cpp11_cpp14_simple_complete_reference.md](../cpp11_cpp14_simple_complete_reference.md), [cpp11_cpp14_complete_reference.md](../cpp11_cpp14_complete_reference.md), [cpp14_new_features_and_constexpr.md](../cpp14_new_features_and_constexpr.md), [modern_cpp_utilities.md](../modern_cpp_utilities.md) |
| [stl_and_templates.md](stl_and_templates.md) | Templates, STL containers, iterators, algorithms and trade-offs | Merged | [cpp_templates_and_stl_complete_reference.md](../cpp_templates_and_stl_complete_reference.md), [cpp_stl_containers_complete_reference.md](../cpp_stl_containers_complete_reference.md), [cpp_stl_algorithms_complete_reference.md](../cpp_stl_algorithms_complete_reference.md), [stl_tradeoffs.md](../stl_tradeoffs.md) |
| [multithreading.md](multithreading.md) | std::thread, synchronization, classic problems, pthreads, Boost.Thread | Merged | [cpp_multithreading_complete_reference.md](../cpp_multithreading_complete_reference.md), [cpp_multithreading_complete_reference_with_example.md](../cpp_multithreading_complete_reference_with_example.md), [pthread_multithreading_complete_reference.md](../pthread_multithreading_complete_reference.md), [pthread_vs_std_thread_vs_boost_thread.md](../pthread_vs_std_thread_vs_boost_thread.md) |
| [interview_preparation.md](interview_preparation.md) | C++ / systems interview Q&A, including SCB-specific advice | Merged | [cpp_interview_questions_answers_with_code.md](../cpp_interview_questions_answers_with_code.md), [scb_cplusplus_interview_questions_answers.md](../scb_cplusplus_interview_questions_answers.md), [scb_skills_and_qualifications.md](../scb_skills_and_qualifications.md) |
| [boost_libraries.md](boost_libraries.md) | Boost libraries overview and Boost.Asio for real-time applications | Sectioned | [usage_of_boost_libraries_in_cpp.md](../usage_of_boost_libraries_in_cpp.md), [boost_asio_real_time_application.md](../boost_asio_real_time_application.md) |
| [posix_ipc_and_sockets.md](posix_ipc_and_sockets.md) | POSIX inter-process communication and sockets | Sectioned | [posix_ipc_complete_reference.md](../posix_ipc_complete_reference.md), [posix_sockets_complete_reference.md](../posix_sockets_complete_reference.md) |
| [data_structures_algorithms_complexity.md](data_structures_algorithms_complexity.md) | Data structures, algorithms, time/space complexity | Sectioned | [data_structures_and_algorithms_complete_reference.md](../data_structures_and_algorithms_complete_reference.md), [programming_complexity_time_space_performance.md](../programming_complexity_time_space_performance.md) |
| [design_patterns_and_code_analysis.md](design_patterns_and_code_analysis.md) | Design patterns in C++ and a manual code-analysis/debugging guide | Sectioned | [cpp_design_patterns_complete_reference.md](../cpp_design_patterns_complete_reference.md), [Manual_Code_Analysis_Guide.md](../Manual_Code_Analysis_Guide.md) |
| [scripting_python_and_bash.md](scripting_python_and_bash.md) | Python and Bash scripting | Sectioned | [python_complete_reference_basic_core_advanced.md](../python_complete_reference_basic_core_advanced.md), [bash_shell_scripting_complete_reference.md](../bash_shell_scripting_complete_reference.md) |

## Suggested Reading Order

1. `cpp_core_and_advanced.md` — language foundations
2. `modern_cpp_11_14.md` — modern language features
3. `stl_and_templates.md` — generic programming and the standard library
4. `multithreading.md` — concurrency
5. `posix_ipc_and_sockets.md`, `boost_libraries.md` — systems and networking
6. `data_structures_algorithms_complexity.md`, `design_patterns_and_code_analysis.md` — problem solving and design
7. `scripting_python_and_bash.md` — supporting skills
8. `interview_preparation.md` — final revision before an interview

## Keeping This Folder in Sync

The combined files are copies. If you edit an original file in the repository root, update the matching combined file as well (or ask for it to be regenerated).
